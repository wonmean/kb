## SSH

## Generate new SSH key

```bash
ssh-keygen -t rsa -b 4096 -C "<email>"
```

## Use key in GitHub

```bash
cat ~/.ssh/id_rsa.pub
```

Copy the public key and add to GitHub > Settings > SSH and GPG keys.

## Change password for existing SSH key

```bash
ssh-keygen -p -f ~/.ssh/id_rsa
```
