# Apt

## Apt update & upgrade

```bash
sudo apt update
sudo apt -y upgrade
sudo apt autoremove
sudo update-initramfs -u -k `uname -r`
```

## Install and remove a specific package

```bash
sudo apt install cuda-toolkit-11-7
sudo apt remove cuda-toolkit-11-7
sudo apt autoremove
```

Installing from a file works similarly.

```bash
sudo apt install ./<deb>
```

## List installed

```bash
apt list --installed
apt list --installed | grep nvidia
```

## Search for a package

```bash
apt search nvidia
```

## List all available versions

```bash
apt-cache policy <package>
```

## Reinstall

```bash
sudo apt install --reinstall nvidia-driver-515
```

## Apt add and remove repository

```bash
sudo apt-add-repository <url>
```

## Add custom public key

This one is for CUDA developer download website for latest CUDA and NVIDIA drivers.

```bash
wget --no-check-certificate https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo apt-key add 7fa2af80.pub
```

## List all keys

```bash
apt-key list
```

## Hold package version

```bash
sudo apt-mark hold libcudnn8
sudo apt-mark hold linux-generic linux-image-generic linux-headers-generic
sudo apt-mark unhold linux-generic linux-image-generic linux-headers-generic
```

## Clean list cache

```bash
sudo apt clean && \
sudo rm -rf /var/lib/apt/lists/*
```
