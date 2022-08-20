# Ubuntu

## Some common commands

```bash
sudo, ls, cd, tree, |, &&, ||
ssh, grep, less, wget, sftp, rsync
hostname, top, which
tar -xzf -C
unzip -d
gzip -dk
who -a
```

## User and password

Create new user and add to sudo.

```bash
sudo adduser <user>
sudo usermod -aG sudo <user>
sudo usermod -aG <group> <user>
sudo ls -la /root
```

Change password.

```bash
sudo passwd <user>
```

If ~/.bashrc doesn't load, check that `~/.profile` exists.

## Disk space

Disks or current directory.

```bash
df -h
du -hd1
du -hd1 --block-size=1G | sort -n
```

## Hardware

List hardware, CPU, VGA, and motherboard info.

```bash
sudo lshw
cat /proc/cpuinfo
lspci -nnk | grep -iA2 vga
sudo dmidecode -t 2
```

Get PCI topology.

```bash
sudo lspci -vtt
```

## Bulk rename tool

```bash
sudo apt install mmv
mmv old\* new\#1
```

## Mount RAM disk

```bash
sudo mkdir /tmp/ramdisk
sudo chmod 777 /tmp/ramdisk
sudo mount -t tmpfs -o size=1024m myramdisk /tmp/ramdisk
mount | tail -n 1
```

## Sensors

```bash
sudo apt install lm-sensors
sudo sensors-detect
sensors
```

## Check errors

System log.

```bash
sudo vi /var/log/syslog
```

Boot time.

```bash
systemd-analyze
```

Another way to check past the kernel and userspace timing.

```bash
dmesg
```

List failed services.

```bash
systemctl --failed
```

## Kernel modules

Kernel modules can be loaded and unloaded.

```bash
sudo modprobe <mod>
sudo modprobe -r <mod>
```

If modules need to be loaded upon boot, add to the following.

```bash
sudo vi /etc/modules
```

## Toggle GUI on boot

```bash
sudo -i
systemctl get-default
systemctl set-default multi-user.target
```

To restore GUI.

```bash
sudo systemctl isolate graphical
sudo systemctl set-default graphical.target
```

## Change alternatives

```bash
sudo update-alternatives --config libcudnn
```

## Disable cloud-init

```bash
sudo touch /etc/cloud/cloud-init.disabled
```

## Run long-running job in background even after logout

Run the job, stop, run background then disown.

```bash
ctrl+z
bg
disown -h
```

## Clear cache

Clear pagecache, dentries and inodes.

```bash
sudo sync && echo 3 > /proc/sys/vm/drop_caches
```
