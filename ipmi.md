# IPMI

## Setting up IPMI

### Install IPMI tools

```bash
sudo apt -y install ipmitool
sudo brew install ipmitool
```

### Check status locally

```bash
sudo ipmitool lan print 1
```

### Set user and password

Channel is 1, user number is 2

```bash
sudo ipmitool user list 1
sudo ipmitool user set name 2 <user>
sudo ipmitool user set password 2 <password>
sudo ipmitool user enable 2
sudo ipmitool user priv 2 0x4 1
sudo ipmitool user list 1
```

### Set HDCP, IP or subnet mask

```bash
sudo ipmitool mc reset cold
sudo ipmitool lan set 1 ipsrc dhcp
sudo ipmitool lan set 1 ipsrc static
sudo ipmitool lan set 1 ipaddr <ip>
sudo ipmitool lan set 1 netmask <mask>
sudo ipmitool lan set 1 defgw ipaddr <gateway ip>
```

### Manually load necessary modules

If IPMI kernels are not loaded automatically, manually load and add to `/etc/modules`.  
[Reference](https://serverfault.com/questions/480371/ipmitool-cant-find-dev-ipmi0-or-dev-ipmidev-0).

```bash
sudo modprobe ipmi_devintf
sudo modprobe ipmi_si
```

## Using IPMI

### Remote control

Simply open the IPMI address on a web browser.  
Same user and password apply.  
Once loaded, use Remote Control > iKVM/HTML5 to remote control the system.

When the system has a discrete GPU installed with an available display output, iKVM may not work.  
On boot, there will be a message "iKVM doesn't support add-on VGA device".  
VGA priority should be set to the onboard ASPEED controller in the BIOS and plugged into a monitor.  
[Reference](https://www.supermicro.com/support/faqs/faq.cfm?faq=22034).

### Check status and power cycle remotely

```bash
ipmitool -H <ip> -U <user> chassis status
ipmitool -H <ip> -U <user> power on
ipmitool -H <ip> -U <user> power soft
ipmitool -H <ip> -U <user> power off
ipmitool -H <ip> -U <user> power cycle
```

Obsolete commands.

```bash
ipmitool -H <ip> -U <user> bootdev disk
ipmitool -H <ip> -U <user> bootdev bios
ipmitool -H <ip> -U <user> bootdev none clear-cmos=yes
```
