# Grub

## List Linux Standard Base (LSB)

```bash
lsb_release -a
lsb_release -s -rdc
```

## List unix name

```bash
uname -r
```

## Display boot option menu entries

```bash
sed -nre "/submenu|menuentry/s/(.? )'([^']+)'.*/\1 \2/p" < /boot/grub/grub.cfg
```

## Modify default boot option

To swap from `5.4.0-70-lowlatency` to `4.15.0-143-generic`, first check the boot options.

```text
menuentry  Ubuntu
submenu  Advanced options for Ubuntu
    menuentry  Ubuntu, with Linux 5.4.0-70-lowlatency
    menuentry  Ubuntu, with Linux 5.4.0-70-lowlatency (recovery mode)
    menuentry  Ubuntu, with Linux 4.15.0-143-generic
    menuentry  Ubuntu, with Linux 4.15.0-143-generic (recovery mode)
menuentry  System setup
```

Edit the grub file.  
Select the advanced menu then the proper boot option.

```bash
sudo vi /etc/default/grub
```

```text
GRUB_DEFAULT="1>2"
```

```bash
sudo update-grub
sudo reboot
```

Check that the correct kernel was loaded.

```bash
uname -r
```
