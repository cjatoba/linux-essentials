## Check swap status
```bash
swapon --show 
```

## Disable swap status
```bash
swapoff -a 
```

## Check swappiness configuration
```bash
sysctl vm.swappiness
```

## Set swappiness configuration

### Open `/etc/sysctl.config` file
```bash
sudo vim /etc/sysctl.config
```

### Add vm.swappiness configuration
```bash
vm.swappiness=10
```

### Apply configuration
```bash
sudo sysctl -p /etc/sysctl.conf
```
