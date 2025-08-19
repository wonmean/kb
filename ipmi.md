# IPMI

## Install IPMI tools
```bash
sudo apt -y install ipmitool
```

## Check status locally
ASMB-816 LAN4, which supports 10G and BMC, is on lan 2.  
LAN5 is on lan 1 but does not support 10G.
```bash
sudo ipmitool lan print 1
```

## Set user and password
Channel is 1, user number is 2
```bash
sudo ipmitool user list 1
sudo ipmitool user set name 2 <user>
sudo ipmitool user set password 2 <password>
sudo ipmitool user enable 2
sudo ipmitool user priv 2 0x4 1
sudo ipmitool user list 1
```

## Set HDCP, IP or subnet mask
```bash
sudo ipmitool mc reset cold
sudo ipmitool lan set 1 ipsrc dhcp
sudo ipmitool lan set 1 ipsrc static
sudo ipmitool lan set 1 ipaddr 10.21.68.22
sudo ipmitool lan set 1 netmask 255.255.254.0
sudo ipmitool lan set 1 defgw ipaddr 10.21.68.1
```

## Manually load necessary modules
If IPMI kernels are not loaded automatically, manually load and add to `/etc/modules`.  
[Reference](https://serverfault.com/questions/480371/ipmitool-cant-find-dev-ipmi0-or-dev-ipmidev-0).
```bash
sudo modprobe ipmi_devintf
sudo modprobe ipmi_si
```

## Remote control
Open the IPMI address on a web browser.  
Same user and password apply.  
Once loaded, use Remote Control > iKVM/HTML5 to remote control the system.

If iKVM shows "Incomplete protocol version, rQ:1002(please do 'iKVM Reset')", then encryption must be turned off.  
Go to Configuration > KVM & Media and select "None" for KVM Encryption.  
You may need to reload or open in incognito to click the save button.

When the system has a discrete GPU installed with an available display output, iKVM may not work.  
On boot, there will be a message "iKVM doesn't support add-on VGA device".  
VGA priority should be set to the onboard ASPEED controller in the BIOS and plugged into a monitor.  
[Reference](https://www.supermicro.com/support/faqs/faq.cfm?faq=22034).

## Check status and power cycle remotely
```bash
ipmitool -H 10.21.33.72 -U genia chassis status
ipmitool -H 10.21.33.72 -U genia power on
ipmitool -H 10.21.33.72 -U genia power soft
ipmitool -H 10.21.33.72 -U genia power off
ipmitool -H 10.21.33.72 -U genia power cycle
```
