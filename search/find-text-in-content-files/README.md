## Search text in files content
```ssh
grep -R "text" local/*
```

## Search for text in command output and show 3 lines after
```ssh
ip a | grep eth0 -A 3
```

## Search for text in command output and show 3 lines before
```ssh
ip a | grep eth0 -B 3
```
