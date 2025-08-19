## Some common commands
```bash
sudo, ls, cd, tree, |, &&, ||, >, >>
head, tail -f
ssh, grep, less, wget, sftp, rsync
find, xargs
top, which
tar -czf, tar -xzf
gzip -k, gzip -dk
zip, unzip
who -a
ps all
top, iotop
vi /etc/passwd
vi /etc/group
```

## User and password
Create new user and add to sudo.
```bash
sudo adduser leew42
```
```bash
sudo usermod -aG sudo leew42
sudo usermod -aG genia leew42
sudo ls -la /root
```
```bash
sudo adduser leew42 docker
sudo deluser leew42 docker
```
```bash
vipw
vigr
visudo
```

Change password.
```bash
sudo passwd leew42
```
If ~/.bashrc doesn't load, check that `~/.profile` exists.

Check password statuses.
```bash
sudo passwd -Sa
```

## Change hostname
```bash
sudo hostnamectl set-hostname <hostname>
```
Also change in `/etc/hosts`.

## Disk space
```bash
df -h
du -hd1
du -hd1 --block-size=1G | sort -n
```

## Check errors
```bash
sudo vi /var/log/syslog
```
```bash
systemd-analyze critical-chain
```
```bash
dmesg
```
```bash
systemctl --failed
```

## Run long-running job in background even after logout
Run the job, stop, run background then disown.
```bash
ctrl+z
bg
disown -h
```

## Kernel modules
```bash
sudo modprobe nsa
```
```bash
sudo modprobe -r nsa
```

If modules need to be loaded upon boot, add to the following.
```bash
sudo vi /etc/modules
```

## Kernel parameters
[Reference](https://docs.kernel.org/admin-guide/sysctl/vm.html).  
Forces keeping more memory free.
```bash
sysctl -w vm.min_free_kbytes=5242880
```
Higher values means a greater probability of the kernel not caching inode objects.
```bash
sysctl -w vm.vfs_cache_pressure=200
```

## Mount RAM disk
```bash
sudo mkdir /tmp/ramdisk
sudo chmod 777 /tmp/ramdisk
sudo mount -t tmpfs -o size=1024m myramdisk /tmp/ramdisk
mount | tail -n 1
```

## Bulk rename tool
```bash
sudo apt install mmv
mmv old\* new\#1
```

## Sensors
```bash
sudo apt install lm-sensors
sudo sensors-detect
sensors
```

## Clear cache
Clear pagecache, dentries, and inodes.
```bash
sudo sync && echo 3 > /proc/sys/vm/drop_caches
```

## Toggle GUI on boot
Check.
```bash
sudo systemctl get-default
```
```bash
sudo systemctl set-default multi-user.target
```

Restore GUI.
```bash
sudo systemctl isolate graphical && \
sudo systemctl set-default graphical.target
```

## Change alternatives
```bash
sudo update-alternatives --config libcudnn
```

## Revert file and folder permissions to default
```bash
sudo find . -type f | sudo xargs chmod 664 && \
sudo find . -type d | sudo xargs chmod 775
```
