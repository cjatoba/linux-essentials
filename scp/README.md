## Copying a remote file to local machine:

```bash
scp user@domain:/remote-folder/remote-file.txt /local-folder/local-file.txt
```

## Sending a local file to a remote server:

```bash
scp /local-folder/local-file.txt user@domain:/remote-folder/remote-file.txt
```

## Copying folders and subfolders from remote server to local machine:

```bash
scp -r user@domain:/remote-folder/ /local-folder/
```

## Sending folders and subfolders from local machine to remote server:

```bash
scp -r /local-folder/ user@domain:/remote-folder/
```

## Sending folders and subfolders from remote machine to another remote server:

```bash
scp -r user@domain:/remote-folder user@domain:/remote-folder/
```
