# Network

## Common commands

Provides current IP address, gateway and nameserver info, respectively.

```bash
ip a
ip r
systemd-resolve --status
```

## Enable DHCP on boot

Add the following to `/etc/rc.local`.

```bash
#!/bin/bash
dhclient
exit 0
```

Add as a service.

```bash
sudo chmod 755 /etc/rc.local
sudo systemctl enable rc-local
sudo systemctl restart rc-local
sudo systemctl status rc-local
```

## Setup netplan

Create a new file at `/etc/netplan/01-netcfg.yaml` or use the pre-existing one if created during OS install.
Using DHCP.

```text
network:
  ethernets:
    eno1:
      dhcp4: true
      optional: true
    eno2:
      dhcp4: true
      optional: true
  version: 2
```

Using static IP.

```text
network:
  ethernets:
    eno1:
      optional: true
    rename3:
      dhcp4: false
      addresses:
        - <ip>/23
      gateway4: <gateway ip>
      nameservers:
        addresses: [<dns ip1>, <dns ip2>]
        search: [<dns>]
  version: 2
```

Apply.

```bash
sudo netplan --debug apply
```

`optional` reduces boot time wait for IP, but may not guarantee SSH.

## Check network log

```bash
sudo journalctl --no-pager -lu systemd-networkd
```

## Check max network card speed

Use `ip a` to find the appropriate network card.

```bash
sudo ethtool rename3
```
