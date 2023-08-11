# How to add fingerprint unlock in XFCE

## Install `fprintd` and `libpam-fprintd`

```bash
sudo apt install fprintd libpam-fprintd
```

## Register fingerprint

```bash
fprintd-enroll <USERNAME>
```

## Verify fingerprint

```bash
fprintd-verify <USERNAME>
```

## System Configuration

Edit `/etc/pam.d/common-auth file` add `auth sufficient pam_fprintd.so` after `# here are the per-package modules (the "Primary" block)` line
```bash
# here are the per-package modules (the "Primary" block)
auth sufficient pam_fprintd.so
...
# here's the fallback if no module succeeds
```
