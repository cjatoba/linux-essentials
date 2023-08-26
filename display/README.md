# Tips for displays

## Change resolution for widescreen monitors:

- Add new ModeLine (*This example works on the LG UltraWide 29WL500 29" led monitor, try other ModeLine if this one doesn't work):
```bash
//add modeline
xrandr --newmode "2560x1080_53.92" 162.5 2560 2608 2640 2720 1080 1083 1093 1108 +HSync -VSync
```
- Check monitor name
```bash
xrandr --listmonitors
```

Output example:

![image](https://github.com/cjatoba/linux-essentials/assets/52887959/4acfc501-8110-44c2-a236-0e6ed3b026a1)

- Add ModeLine on monitor:
```bash
xrandr --addmode HDMI-1 2560x1080_53.92
```
