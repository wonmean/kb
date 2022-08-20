# NVIDIA

## NVIDIA current instructions

Now that GDS comes packaged with the CUDA 11.4 deb installer, our workflow is much simpler.  
https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#ubuntu-installation

## Check current GPUs

```bash
lspci -nnk | grep -iA2 vga
sudo lshw -C display | grep product
```

## Add CUDA developer repository

We have to add their apt repo key, repo location and prioritize it over the default ubuntu repo.
Key was last updated as of 2022-04-28 (`7fa2af80` -> `3bf863cc.pub`).

```bash
sudo apt-key del 7fa2af80 && \
sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/3bf863cc.pub
```

Add NVIDIA's CUDA repository.

```bash
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /" && \
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin && \
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600 && \
sudo apt update
```

Equivalent for `20.04`.

```bash
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/ /" && \
wget http://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin && \
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600 && \
sudo apt update
```

To remove, run the following.

```bash
sudo apt-key del 7fa2af80 && \
sudo add-apt-repository -r "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /" && \
sudo rm /etc/apt/preferences.d/cuda-repository-pin-600 && \
sudo apt update
```

## Install

Install NVIDIA driver.

```bash
sudo apt -y install nvidia-driver-515
```

Install CUDA.

```bash
sudo apt -y install cuda-toolkit-11-6
```

Install and hold cuDNN.

```bash
sudo apt-mark unhold libcudnn8 libcudnn8-dev && \
sudo apt -y --allow-downgrades install libcudnn8-dev=8.4.0.27-1+cuda11.6 libcudnn8=8.4.0.27-1+cuda11.6 && \
sudo apt-mark hold libcudnn8 libcudnn8-dev
```

Install and hold TensorRT.

```bash
sudo apt-mark unhold libnvinfer8 libnvinfer-dev && \
sudo apt -y --allow-downgrades install libnvinfer-dev=8.2.4-1+cuda11.4 libnvinfer8=8.2.4-1+cuda11.4 && \
sudo apt-mark hold libnvinfer8 libnvinfer-dev
```

## Add NVIDIA driver streaming option

```bash
sudo echo "options nvidia NVreg_EnableStreamMemOPs=1" > /etc/modules-load.d/nvidia.conf
sudo modprobe -r nvidia_uvm nvidia_drm nvidia_modeset nvidia
nvidia-smi
```

## Add directories to bashrc

This may now be optional.
Add the following to `~/.bashrc`.

```bash
export PATH="/usr/local/cuda/bin:$PATH"
```

## Check installation

```bash
nvidia-smi
nvcc --version
cat /usr/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
cat /usr/include/x86_64-linux-gnu/NvInferVersion.h | grep NV_TENSORRT_MAJOR -A 3
```

## Monitor GPU usage

```bash
nvidia-smi -l
```

If you have a corrupted infoROM, you will get this warning at the end of `nvidia-smi`.

```text
WARNING: infoROM is corrupted at gpu 0000:03:00.0
```

Specify a particular GPU to use monitoring.

```bash
nvidia-smi -i 1 -l
```

Check NVLink status.

```bash
nvidia-smi nvlink --status -i 0
nvidia-smi topo -m
```

## Enable persistence mode

```bash
sudo nvidia-smi -pm 1
```

## Enable GPU metrics in remote Nsight Systems

Add the following to `/etc/modprobe.d/nvidia.conf`.

```bash
options nvidia NVreg_RestrictProfilingToAdminUsers=0
```

## Enable streaming memory ops

Add the following to `/etc/modprobe.d/nvidia.conf`.

```bash
options nvidia NVreg_EnableStreamMemOPs=1
```

## Specify a GPU to use

```bash
export CUDA_VISIBLE_DEVICES=0
```

## Turn off TensorCore V3 TF32

```bash
export NVIDIA_TF32_OVERRIDE=0
```

## Specify PCI ordering for CUDA device enumeration

This does not impact `nvidia-smi`, which already uses PCI ordering.

```bash
CUDA_DEVICE_ORDER=PCI_BUS_ID ./app --check
```

## Test p2p bandwidth

```bash
cp -r /usr/local/cuda-11.5/samples .
cd samples/1_Utilities/p2pBandwidthLatencyTest
make
./p2pBandwidthLatencyTest
```

## Remove diversion binaries for old driver installs

The error message on install was the following.

```text
diversion of /usr/lib/x86_64-linux-gnu/libGL.so.1 to /usr/lib/x86_64-linux-gnu/libGL.so.1.distrib by nvidia-340
dpkg-divert: error: mismatch on package
  when removing 'diversion of /usr/lib/x86_64-linux-gnu/libGL.so.1 by libnvidia-gl-495'
  found 'diversion of /usr/lib/x86_64-linux-gnu/libGL.so.1 to /usr/lib/x86_64-linux-gnu/libGL.so.1.distrib by nvidia-340'
dpkg: error processing archive /tmp/apt-dpkg-install-IdzZur/07-libnvidia-gl-495_495.29.05-0ubuntu1_amd64.deb (--unpack):
 new libnvidia-gl-495:amd64 package pre-installation script subprocess returned error exit status 2
```

Search found this.  
https://askubuntu.com/questions/1035409/installing-nvidia-drivers-on-18-04

Checking.

```bash
$ LC_MESSAGES=C dpkg-divert --list '*nvidia-340*'
diversion of /usr/lib/x86_64-linux-gnu/libEGL.so to /usr/lib/x86_64-linux-gnu/libEGL.so.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libGLESv2.so.2 to /usr/lib/i386-linux-gnu/libGLESv2.so.2.distrib by nvidia-340
diversion of /usr/lib/x86_64-linux-gnu/libGLESv2.so.2 to /usr/lib/x86_64-linux-gnu/libGLESv2.so.2.distrib by nvidia-340
diversion of /usr/lib/x86_64-linux-gnu/libEGL.so.1 to /usr/lib/x86_64-linux-gnu/libEGL.so.1.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libGLESv2.so to /usr/lib/i386-linux-gnu/libGLESv2.so.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libEGL.so to /usr/lib/i386-linux-gnu/libEGL.so.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libGL.so to /usr/lib/i386-linux-gnu/libGL.so.distrib by nvidia-340
diversion of /usr/lib/x86_64-linux-gnu/libGL.so.1 to /usr/lib/x86_64-linux-gnu/libGL.so.1.distrib by nvidia-340
diversion of /usr/lib/x86_64-linux-gnu/libGL.so to /usr/lib/x86_64-linux-gnu/libGL.so.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libEGL.so.1 to /usr/lib/i386-linux-gnu/libEGL.so.1.distrib by nvidia-340
diversion of /usr/lib/x86_64-linux-gnu/libGLESv2.so to /usr/lib/x86_64-linux-gnu/libGLESv2.so.distrib by nvidia-340
diversion of /usr/lib/i386-linux-gnu/libGL.so.1 to /usr/lib/i386-linux-gnu/libGL.so.1.distrib by nvidia-340
```

Removing.

```bash
$ LC_MESSAGES=C dpkg-divert --list '*nvidia-340*' | sed -nre 's/^diversion of (.*) to .*/\1/p' | xargs -rd'\n' -n1 -- sudo dpkg-divert --remove
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libEGL.so to /usr/lib/x86_64-linux-gnu/libEGL.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libGLESv2.so.2 to /usr/lib/i386-linux-gnu/libGLESv2.so.2.distrib by nvidia-340'
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libGLESv2.so.2 to /usr/lib/x86_64-linux-gnu/libGLESv2.so.2.distrib by nvidia-340'
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libEGL.so.1 to /usr/lib/x86_64-linux-gnu/libEGL.so.1.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libGLESv2.so to /usr/lib/i386-linux-gnu/libGLESv2.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libEGL.so to /usr/lib/i386-linux-gnu/libEGL.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libGL.so to /usr/lib/i386-linux-gnu/libGL.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libGL.so.1 to /usr/lib/x86_64-linux-gnu/libGL.so.1.distrib by nvidia-340'
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libGL.so to /usr/lib/x86_64-linux-gnu/libGL.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libEGL.so.1 to /usr/lib/i386-linux-gnu/libEGL.so.1.distrib by nvidia-340'
Removing 'diversion of /usr/lib/x86_64-linux-gnu/libGLESv2.so to /usr/lib/x86_64-linux-gnu/libGLESv2.so.distrib by nvidia-340'
Removing 'diversion of /usr/lib/i386-linux-gnu/libGL.so.1 to /usr/lib/i386-linux-gnu/libGL.so.1.distrib by nvidia-340'
```

## Switch between CUDA versions

```bash
sudo update-alternatives --config cuda
```

## Obsolete instructions

### Remove in-use modules

```bash
sudo service gdm stop
sudo service lightdm stop
vncserver -kill :1
```

If necessary, unload nvidia-drm.

```bash
sudo -i
systemctl isolate multi-user.target
rmmod nvidia-drm
exit
```

If necessary, uninstall using a driver run file.
Reboot to unload the nvidia modules.

```bash
sudo ./NVIDIA-Linux-x86_64-455.23.04.run --silent --uninstall
sudo reboot
```

If necessary, remove all apt nvidia modules.

```bash
sudo apt remove --purge nvidia*
sudo apt remove --purge libnvidia*
sudo apt autoremove
```

If necessary, uninstall nvidia-driver using the uninstall script.

```bash
sudo /usr/bin/nvidia-uninstall
```

If necessary, uninstall CUDA using the uninstall script.

```bash
sudo /usr/local/cuda/bin/cuda-uninstaller
```

### Blacklist nouveau and nvidiafb drivers

`nvidia-driver-455` and `nvidia-driver-465` seem to do this automatically by adding `/etc/modprobe.d/nvidia-graphics-drivers.conf`.

```text
A modprobe blacklist file has been created at /etc/modprobe.d to prevent Nouveau
from loading. This can be reverted by deleting the following file:
/etc/modprobe.d/nvidia-graphics-drivers.conf
```

To do manually, add the following to `/etc/modprobe.d/blacklist.conf`.

```bash
blacklist nouveau
blacklist nvidiafb
```
