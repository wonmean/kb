## Download Ubuntu ISO
```bash
wget https://releases.ubuntu.com/jammy/ubuntu-22.04.5-live-server-amd64.iso
```

## Check for USB drive
Important, since writing the ISO will wipe the target drive.
```bash
$ lsblk -S
NAME HCTL       TYPE VENDOR   MODEL            REV SERIAL           TRAN
sda  0:0:0:0    disk Lexar    USB Flash Drive 1100 0177002023800354 usb
```

On MacOS:
```bash
% diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            10.2 GB    disk1s1
   2:              APFS Snapshot com.apple.os.update-... 10.2 GB    disk1s1s1
   3:                APFS Volume Macintosh HD - Data     160.0 GB   disk1s2
   4:                APFS Volume Preboot                 2.3 GB     disk1s3
   5:                APFS Volume Recovery                1.2 GB     disk1s4
   6:                APFS Volume VM                      7.5 GB     disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     Apple_partition_scheme                        *123.0 GB   disk2
   1:        Apple_partition_map                         4.1 KB     disk2s1
   2:                  Apple_HFS                         4.2 MB     disk2s2
                    (free space)                         123.0 GB   -
```

## Write ISO
```bash
sudo umount /dev/disk2
```
```bash
sudo dd if=ubuntu-22.04.5-live-server-amd64.iso of=/dev/disk2 bs=4M status=progress
```
