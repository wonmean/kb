# Mellanox

## Find cards
```bash
sudo lshw -c network -businfo
```

## Install firmware
Download from [here](https://support.hpe.com/connect/s/softwaredetails?language=en_US&softwareId=MTX_50cbcf58f5f3422f81f86f6e2b&collectionId=MTX-23c569ea92fb47f0&tab=releaseNotes).  
Upload to ILO and install.

## Install MLNX_OFED drivers
Download from [here](https://network.nvidia.com/products/infiniband-drivers/linux/mlnx_ofed/) for the appropriate OS.  
As of writing, the latest version (24.10-2.1.8.0-LTS) for Ubuntu 22.04 was deployed.  
Use the x86_64 and tgz file.  
You may need to download it on your laptop and rsync to the system.
```bash
FILE=MLNX_OFED_LINUX-24.10-0.7.0.0-ubuntu20.04-x86_64.tgz \
&& tar -xzf $FILE.tgz \
&& cd $FILE \
&& sudo ./mlnxofedinstall --force --without-ucx-cuda
```

## Check driver version
```bash
sudo modinfo mlx5_core
```

## Checking card info
[Reference](https://enterprise-support.nvidia.com/s/article/getting-started-with-connectx-5-100gb-s-adapters-for-linux).
```bash
sudo mst start
```
Check for devices.
```bash
ls /dev/mst
```
Physical connection.
```bash
sudo mlxlink -d /dev/mst/mt4125_pciconf0 -m
```
Bit error rate.
```bash
sudo mlxlink -d /dev/mst/mt4125_pciconf0 -c
```
Bit error histogram.
```bash
sudo mlxlink -d /dev/mst/mt4125_pciconf0 --rx_fec_histogram --show_histogram
```

Buffer sizes, flow control, interrupt coalescence, and IRQ balance.
```bash
mlnx_qos -i ens3f0np0 | grep buffer && \
ethtool -a ens3f0np0 | grep RX && \
ethtool -c ens3f0np0 | grep RX && \
systemctl status irqbalance | grep Active | sed -e 's/^[ \t]*//g' -e 's/[ \t]*$//g'
```
Config.
```bash
sudo mlxconfig -d /dev/mst/mt4125_pciconf0 q
```

## Change port type
```bash
sudo mlxconfig -d /dev/mst/mt4123_pciconf0 set LINK_TYPE_P1=2 LINK_TYPE_P2=2
```
```bash
sudo mlxconfig -d /dev/mst/mt4123_pciconf1 set LINK_TYPE_P1=2 LINK_TYPE_P2=2
```

## Reset BER counters
```bash
sudo mlxlink -d /dev/mst/mt4125_pciconf1 --rx_fec_histogram --clear_histogram | egrep 'Clearing'
```

## Bring up interfaces
```bash
sudo ip a add 10.1.1.27/24 dev ens3f0np0 && \
sudo ip link set ens3f0np0 mtu 9600 && \
sudo ip link set ens3f0np0 up && \
sudo ip a add 10.1.2.27/24 dev ens6f0np0 && \
sudo ip link set ens6f0np0 mtu 9600 && \
sudo ip link set ens6f0np0 up
```

## Enable SR-IOV
Only necessary for a loopback test.  
Also requires it to be enabled in BIOS.
```bash
sudo mlxconfig -d /dev/mst/mt4123_pciconf0 set SRIOV_EN=1
```

## Resources
[MLX Link Utility](https://docs.nvidia.com/networking/display/mftv422/mlxlink+utility#src-99400530_mlxlinkUtility-mlxlinkUsage).  
[Ethtool Counters](https://enterprise-support.nvidia.com/s/article/understanding-mlx5-ethtool-counters).  
[Configuring and tuning  HPE ProLiant Servers for  low-latency applications](https://www.hpe.com/psnow/doc/c01804533).
