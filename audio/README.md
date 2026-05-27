# [Fix] Linux audio disappears after inactivity — PipeWire automatically suspends ALSA devices (WirePlumber suspend-timeout)

**Keywords:** linux no audio, audio devices disappear, no audio device found, pipewire suspend, wireplumber suspend-timeout, audio stops working linux, chrome no audio linux, teams no audio linux, audio gone after inactivity, wpctl status empty, alsa suspended

---

## The problem

On Linux systems running PipeWire, audio devices can disappear from system settings and audio can stop working after a brief period of inactivity. This can happen in different situations:

- Pausing a video in Chrome for a few seconds and resuming
- Leaving a Teams meeting and joining another one
- Switching tabs or applications while audio was active
- Any scenario where the audio stream is briefly interrupted

---

## Why this happens

### Device suspension is default behavior

Automatic audio device suspension is an **intentional feature, enabled by default** in WirePlumber (the PipeWire session manager). It is a power-saving measure: keeping an audio device open continuously consumes CPU — the ALSA driver stays in a processing loop even when no sound is being played. On battery-powered laptops this has a real impact.

WirePlumber monitors all audio nodes and, after **5 seconds with no active stream**, sends a `Suspend` command to the device, putting the ALSA driver into a low-power state.

### Why audio does not restore automatically

In theory, PipeWire should wake the device automatically when a new stream tries to use it. In practice, this fails in applications like Chrome and Teams due to a combination of reasons:

1. **Corrupted state cache:** when Chrome or Teams connects to the audio device, it stores the connection state internally. When the device is suspended, the connection is not formally terminated — it remains in a corrupted state. When the user resumes the video or joins a new meeting, the application tries to reuse the old (broken) connection instead of opening a new one.

2. **Compatibility layer:** Chrome and Teams communicate with PipeWire via the PulseAudio API (through `pipewire-pulse`). This compatibility layer does not always properly propagate suspend/resume events to the application, which then does not know it needs to reconnect.

3. **Race condition:** when the user resumes content, the application sends audio to PipeWire before the device finishes waking up. This send silently fails and the application does not retry.

### Why devices disappeared from audio settings

Suspension alone does not remove devices from the list. What caused the disappearance was a cascade of failures:

1. Device goes idle → WirePlumber suspends after 5 seconds
2. Chrome or Teams tries to use audio and fails to communicate with the suspended device
3. The application starts sending repeated errors to `pipewire-pulse`
4. `pipewire-pulse` enters a corrupted state
5. GNOME Settings queries devices via `pipewire-pulse` — which in that state responds with an empty list
6. Result: no devices shown in settings, even though PipeWire is still running

The devices did not disappear from PipeWire itself, but the compatibility layer bridging PipeWire to GNOME was corrupted and no longer reported them correctly. This is why restarting only Chrome or Teams did not fix it — the problem was in `pipewire-pulse`, and was only resolved by restarting the audio services or rebooting.

---

## The solution

The fix disables automatic suspension for all ALSA devices via a WirePlumber rule, using the correct property name the system reads internally.

> **Important:** the correct property name is `session.suspend-timeout-seconds`. A common mistake is using `session.suspend-timeout` (without the `-seconds` suffix), but the WirePlumber suspend script (`suspend-node.lua`) only reads the suffixed version — the value is ignored and suspension continues.

---

## Prerequisite: verify the system uses PipeWire + WirePlumber

```bash
systemctl --user status pipewire wireplumber
```

If both appear as `active (running)`, this solution applies. To check the installed versions:

```bash
pipewire --version
wireplumber --version
```

> This solution was tested on WirePlumber 0.4.x. On older versions the suspend mechanism may differ.

---

## Steps

### 1. Remove incorrect configs (if they exist)

```bash
rm -f ~/.config/pipewire/pipewire.conf.d/disable-suspend.conf
rm -f ~/.config/pipewire/pipewire.conf.d/usb-stable.conf
```

> **Warning:** do not place `libpipewire-module-protocol-pulse` in configs for the main PipeWire process — this module is already loaded by `pipewire-pulse.service` and causes a socket conflict, taking down the entire audio stack.

### 2. Create the correct WirePlumber config

```bash
mkdir -p ~/.config/wireplumber/main.lua.d

cat > ~/.config/wireplumber/main.lua.d/51-disable-suspend.lua << 'EOF'
table.insert(alsa_monitor.rules, {
  matches = {
    {
      { "node.name", "matches", "alsa_*" },
    },
  },
  apply_properties = {
    ["session.suspend-timeout-seconds"] = 0,
  },
})
EOF
```

### 3. Restart the services

```bash
systemctl --user reset-failed
systemctl --user restart pipewire pipewire-pulse wireplumber
```

### 4. Verify it is working

```bash
wpctl status
```

The command should list audio devices normally:

```
Audio
 ├─ Devices:
 │      XX. Built-in Audio     [alsa]
 ├─ Sinks:
 │  *   XX. Built-in Audio Analog Stereo   [vol: 1.00]
```

If the list is empty, run `journalctl --user -u wireplumber -n 30` to check for errors.

### 5. Restart audio applications

After applying the fix, restart Chrome, Teams, or any other audio application — they do not automatically reconnect to the device if the stream was already in a broken state.

---

## How the solution works

### What the Lua script does — line by line

```lua
table.insert(alsa_monitor.rules, {
```
`alsa_monitor.rules` is the list of behavior rules WirePlumber maintains for ALSA devices. `table.insert` appends a new rule to the end of that list without removing existing system rules.

```lua
  matches = {
    {
```
Defines the conditions for the rule to be applied. The double nesting exists because multiple condition groups are supported — if any group matches, the rule is applied. Here there is only one group.

```lua
      { "node.name", "matches", "alsa_*" },
```
The condition itself: three elements — the property to check (`node.name`), the operator (`matches`, which accepts glob patterns), and the value (`alsa_*`). The asterisk is a wildcard representing any text. This applies the rule to **all ALSA audio devices on the machine**, without needing to know the specific hardware name.

```lua
  apply_properties = {
    ["session.suspend-timeout-seconds"] = 0,
  },
```
Defines what to do on nodes that match the conditions: apply the property `session.suspend-timeout-seconds` with value `0`. This is exactly the property the WirePlumber internal script `suspend-node.lua` checks before suspending a device. When the value is `0`, the script skips suspension and schedules no timer.

### When the script runs and how the config persists

The script is **not executed once** — it is loaded and executed by WirePlumber every time the service starts:

1. System boots or `systemctl --user restart wireplumber` is run
2. WirePlumber loads all `.lua` files from `main.lua.d/` — both system ones (`/usr/share/wireplumber/main.lua.d/`) and user ones (`~/.config/wireplumber/main.lua.d/`)
3. The script runs, inserting the rule into `alsa_monitor.rules`
4. Whenever a new ALSA device is detected, WirePlumber iterates through all rules and applies properties to matching devices

The configuration is **not written to the device or the kernel** — it only exists while WirePlumber is running. What persists is the `.lua` file at `~/.config/wireplumber/main.lua.d/51-disable-suspend.lua`. Because it is in that folder, it is loaded automatically on every boot, making the behavior permanent as long as the file exists.

If the file is removed and WirePlumber restarted, suspension would return to the default 5-second behavior.
