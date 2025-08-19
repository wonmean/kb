# Keys

## Generate new ssh key
```bash
ssh-keygen -t rsa -b 4096 -C won-mean.lee@roche.com
```

## Use key in GitHub
```bash
cat ~/.ssh/id_rsa.pub
```
Copy the public key and add to GitHub > Settings > SSH and GPG keys.

## Use key in workstations
Copy the public key and add to `~/.ssh/authorized_keys`.

## Change password for existing ssh key
```bash
ssh-keygen -p -f ~/.ssh/id_rsa
```
