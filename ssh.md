## Increase SSH timeout
Modify these two lines, `120s x 30 = 3600s = 1hr`.  
Or run the following to replace.
```
sudo sed -i -e 's/#ClientAliveInterval 0/ClientAliveInterval 120/g' -e 's/#ClientAliveCountMax 3/ClientAliveInterval 30/g' /etc/ssh/sshd_config
```

## Add additional listening ports
Add more ports to `/etc/ssh/sshd_config`.
```bash
Port 22
Port 2201
Port 2202
Port 2203
Port 2204
Port 2205
```
Restart the service
```bash
sudo systemctl restart ssh
```

## Allow sudo from ssh
Make sure the pub key is in `.ssh/authorized_keys`.
```bash
sudo visudo
```
Add the following.
```bash
Cmnd_Alias      WON_CMDS = /sbin/insmod, /sbin/rmmod, /usr/sbin/service, /usr/sbin/smartctl, /usr/sbin/shutdown, /usr/bin/reboot
#wonmean ALL=(ALL)  NOPASSWD: WON_CMDS
wonmean ALL=(ALL)  NOPASSWD: ALL
```

## Allow systemctl management
```bash
sudo vi /etc/polkit-1/localauthority/50-local.d/10-manage-units-ubuntu.pkla
```
```bash
[Allow users to manage services]
Identity=unix-group:root;unix-group:wonmean
Action=org.freedesktop.systemd1.manage-units
ResultAny=yes
ResultInactive=yes
ResultActive=yes
```
