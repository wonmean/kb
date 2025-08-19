# Hardware

## List all hardware
```bash
sudo lshw | less
```

## CPU
```bash
lscpu
```

More details.
```bash
cat /proc/cpuinfo | less
```

## Memory
```bash
free
```

More details.
```bash
sudo dmidecode --type memory | less
```

## Motherboard
```bash
sudo dmidecode -t 2
```

## PCI devices
```bash
lspci | less
```

Topology.
```bash
lspci -vt | less
```

Topology diagram.
```bash
lstopo --of png > server.png
```

## Network
This maps the bus and device IDs.
```bash
sudo lshw -c network -businfo
```

## VGA
```bash
lspci -nnk | grep -iA2 vga
```

## Disks
```bash
df -h
lsblk
```

See [drives.md](drives.md) for more info.
