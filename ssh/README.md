## SSH key generate

```bash
ssh-keygen -t rsa -C "your_email@example.com"

#Or in Old Systems

ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

## Multiple ssh keys

- Generate your secondary ssh key:

```bash
ssh-keygen -t rsa -C "your_secondary_email@example.com"
```

- Insert path and new name for secondary ssh key (Replace your_user_name with your real username):

```
Generating public/private rsa key pair.
Enter file in which to save the key (/home/your_user_name/.ssh/id_rsa): /home/your_user_name/.ssh/your_custom_new_secondary_key_name_rsa
```

- Create config file in ~/your_user_name/.ssh/

```bash
vim ~/your_user_name/.ssh/config
```

- Insert below content (Replace git.first_domain.local and github.com with real server names)

```bash
Host *git.first_domain.local
    HostName git.first_domain.local
    IdentityFile ~/.ssh/id_rsa
    User your_user_name_in_first_domain

Host *github.com
  Hostname github.com
  IdentityFile ~/.ssh/your_custom_new_secondary_key_name_rsa
  User your_user_name_in_secondary_domain
```

## SSH cli access

```bash
ssh user@10.0.1.1

#Or for specific port

ssh user@10.0.1.1 -p 3456
```

## SSH gui access:

```bash
ssh -X user@10.0.1.1
```
