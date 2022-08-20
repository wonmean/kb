# Drives

## List

Disk usage.

```bash
du -h
du -h -d 1
```

Partitions.

```bash
df -h
lsblk
```

Mounted ext4 partitions.

```bash
sudo mount | grep ext4
```

Drives.

```bash
sudo parted -l | grep Model -A 2
sudo blkid
```

Block size.

```bash
sudo fdisk -l
cat /sys/class/block/sda/queue/logical_block_size
```

## Mount partition

```bash
sudo mkdir /mnt/nvme1
sudo mount /dev/nvme1n1 /mnt/nvme1
```

## Auto-mount partition

Use /dev or UUID.

```bash
sudo vi /etc/fstab
```

Add an entry with device/UUID, mount point, partition type, backup, and fsck check order.

```bash
/dev/md0 /mnt/raid_0 ext4 defaults 0 2
/dev/md1 /mnt/raid_1 ext4 defaults 0 2
```

## Create new partition table and partition

```bash
sudo parted
select /dev/nvme1n1
print
mklabel gpt
mkpart primary ext4 0MB 500GB
quit
```

## Create new filesystem

```bash
sudo mkfs.ext4 /dev/nvme1n1
sudo mkfs.ext4 /dev/md0
sudo mkfs.ext4 /dev/md1
```

## Create RAIDs

RAID info.

```bash
cat /proc/mdstat
```

Unmount and stop RAID if needed.

```bash
umount /dev/md0
sudo mdadm --stop /dev/md0
```

Clean metadata.

```bash
sudo mdadm --zero-superblock /dev/sda
sudo mdadm --zero-superblock /dev/sdb
```

Create new RAID.

```bash
sudo mdadm --create --verbose /dev/md0 --level=0 --raid-devices=2 /dev/sda /dev/sdb
```

Create new partition and mount.  
Add to `/etc/fstab` for automount.  
If the new drive does not mount, restart after setting up automount.

```bash
sudo mkfs.ext4 -F /dev/md0
sudo mkdir /mnt/raid
sudo mount /dev/md0 /mnt/raid
```

Update mdadm config.

```bash
sudo -i
mdadm --detail --scan >> /etc/mdadm/mdadm.conf
update-initramfs -u
```

## Encrypt external hard drive

Create new partition table and filesystem prior to this step.  
`cryptsetup` is necessary.

```bash
sudo apt install cryptsetup
```

Create a passphrase and new filesystem in the encrypted partition.  
Replace `$1` with the target partition.  
Answer `YES` with all capital letters for the passphrased format.

```bash
sudo cryptsetup -v --verify-passphrase luksFormat $1
sudo cryptsetup luksOpen $1 encrypted
sudo mkfs.ext4 /dev/mapper/encrypted
sudo cryptsetup luksClose /dev/mapper/encrypted
```

Open and mount.

```bash
sudo cryptsetup luksOpen /dev/sda1 encrypted
sudo mount /dev/mapper/encrypted /mnt/ext-drive
```

Unmount and close.

```bash
sudo umount /mnt/ext-drive
sudo cryptsetup luksClose /dev/mapper/encrypted
```

## Secure delete a storage device

Use `lsblk` to find a device, not a partition.

```bash
sudo shred -v -n1 -z /dev/sd<letter>
```
