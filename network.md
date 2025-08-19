## Common commands

Provides current IP address, gateway and nameserver info, respectively.
```bash
ip a
ip r
systemd-resolve --status
lshw -class network -businfo
```

Check max network card speed.
```bash
sudo ethtool eno0
```

Check network log.
```bash
sudo journalctl --no-pager -lu systemd-networkd
```

Find connection used by a process.
```bash
sudo lsof -ai -p <process>
```

Trace network.
```
tracepath <address>
```

Check bonds.
```bash
cat /proc/net/bonding/*
```

Check networks.
```bash
cat /etc/systemd/network/bond*
```

## Use ip and ifconfig
```bash
sudo ip a add 10.1.1.27/24 dev ens3f0np0
sudo ip link set ens3f0np0 up
```
```bash
ifconfig
```

## Scan local network
```bash
sudo arp-scan --localnet
```

## Set up cloud-init / netplan

Using static IP.  
HPE DL380a Gen12 `acgdl04` using `/etc/cloud/cloud.cfg.d/90-installer-network.cfg`.
```
# This is the network config written by 'subiquity'
network:
  bonds:
    bond0:
      addresses:
      - 10.164.160.140/24
      interfaces:
      - enP1s3f0np0
      - ens6f0np0
      nameservers:
        addresses:
        - 128.137.24.6
        - 162.132.78.52
        search:
        - acgt.sc1.science.roche.com
        - rsc.roche.com
      parameters:
        mode: active-backup
      routes:
      - to: default
        via: 10.164.160.1
  ethernets:
    enP1s3f0np0: {}
    enP1s3f1np1: {}
    ens6f0np0: {}
    ens6f1np1: {}
  version: 2
```

HPE DL380a Gen 11 `sc1fuji10` using `/etc/cloud/cloud.cfg.d/90-installer-network.cfg`.
```
# This is the network config written by 'subiquity'
network:
  bonds:
    bond0:
      addresses:
      - 10.164.160.82/24
      interfaces:
      - ens3f0np0
      - ens6f0np0
      nameservers:
        addresses:
        - 128.137.24.6
        - 162.132.78.52
        search:
        - acgt.sc1.science.roche.com
        - rsc.roche.com
      parameters:
        mode: active-backup
      routes:
      - to: default
        via: 10.164.160.1
  ethernets:
    ens3f0np0: {}
    ens3f1np1: {}
    ens6f0np0: {}
    ens6f1np1: {}
  version: 2
```

Using DHCP on `rfs-1`.  
X11, which has 1G and 5G, had problems with DHCP due to NIC renaming.  
Set NIC names explicitly by mac addresses.
```
# This is the network config written by 'subiquity'
network:
  ethernets:
    eth1g:
      match:
        macaddress: 0c:c4:7a:e7:d2:0e
      set-name: eth1g
      dhcp4: false
      addresses: [ 10.21.69.98/23 ]
      gateway4: 10.21.68.1
      nameservers:
        addresses: [ 128.137.24.6, 162.132.78.52 ]
        search: [ rsc.roche.com ]
    eth5g:
      match:
        macaddress: ac:1f:6b:8b:18:62
      set-name: eth5g
      dhcp4: true
      optional: true
  version: 2
```

Ubuntu 22.04 uses `cloud-init` that generates netplan config file.  
Use the following to regenerate a netplan config and apply.  
Use `tmux` or `nohup &`.  
Make sure you have a backup plan, e.g. iLO or ipmi, for if SSH connectivity is lost.
```bash
sudo rm /var/lib/cloud/data/instance-id \
&& sudo cloud-init init --local \
&& sudo netplan apply
```
```bash
sudo nohup netplan --debug apply &
```

`optional` reduces boot time wait for IP, but may not guarantee SSH.  
It should only be used for unused NICs.

## Create bond
Use netplan to bond two interfaces. [Reference](https://netplan.readthedocs.io/en/latest/netplan-yaml/#properties-for-device-type-bonds).
```bash
network:
  bonds:
    bond0:
      interfaces: [ ens3f1np1, ens6f1np1 ]
      addresses: [ 10.10.1.1/24 ]
      parameters:
        mode: 802.3ad
```
Apply as above.  
SC1 uses `active-backup`.

Check.
```bash
cat /proc/net/bonding/bond0
```

When using active-backup, the wrong interface maybe active at first.
```text
Bonding Mode: fault-tolerance (active-backup)
Primary Slave: None
Currently Active Slave: ens6f1np1
```

Use this command to change the active interface.
```bash
sudo ip link set dev bond0 type bond active_slave ens3f1np1
```

Use this command to delete a bond.
```bash
sudo ip link delete dev bond0
```
