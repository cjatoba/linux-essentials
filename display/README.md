# Tips for displays

## Change resolution for widescreen monitors:

- Add new ModeLine:
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

- Add ModeLine in monitor:
```bash
xrandr --addmode HDMI-1 2560x1080_53.92
```
