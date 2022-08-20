# SSH

## Increase SSH timeout

```bash
sudo vi /etc/ssh/sshd_config
```

Modify these two lines, `120s x 30 = 3600s = 1hr`.

```text
ClientAliveInterval 120
ClientAliveCountMax 30
```
