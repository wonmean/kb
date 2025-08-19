## Create a USB boot disk
Download ISO from [ubuntu](https://releases.ubuntu.com/22.04/).  
Use the server version unless the desktop environment is necessary.

On Windows, use [Rufus](https://rufus.ie/en_US/) to create a boot disk.

On Mac, use the [following instructions](https://ubuntu.com/tutorials/create-a-usb-stick-on-macos#1-overview).

## Boot from disk and install
[Official tutorial](https://ubuntu.com/tutorials/install-ubuntu-server#1-overview).  
Use default genia user and password.  
Double-check that only the system disk is formatted anew.

## Create the data disk (DL380a)
During the Ubuntu installation, create a RAID 0 array using the data storage devices.  
Mount it to `/home` for simplicity.  
Reboot after installation.  
See [here for more details](drives.md#create-raid).

## Grub options for H100s (DL380a)
Update grub option. See [here](grub.md#update-grub-option-for-dl380a-systems).  

## New user
Create a new user and give sudo permission.
```bash
sudo adduser leew42
```
```bash
sudo usermod -aG sudo leew42 \
&& sudo usermod -aG genia leew42
```
Log in as user.

## Start tmux
This is to make sure any of the apt installation isn't interrupted by ssh connection loss.
```bash
tmux
```

## Set miscellaneous settings.
- Make sure system can't suspend or sleep.
- Disable automatic updates.
- Set SSH timeout to 60 minutes.
- Set timezone to LA.
```bash
sudo systemctl set-default multi-user.target \
&& sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target \
&& sudo cp /etc/apt/apt.conf.d/20auto-upgrades /etc/apt/apt.conf.d/20auto-upgrades.save \
&& sudo sed -i 's/1/0/g' /etc/apt/apt.conf.d/20auto-upgrades \
&& sudo sed -i -e 's/#ClientAliveInterval 0/ClientAliveInterval 120/g' \
-e 's/#ClientAliveCountMax 3/ClientAliveInterval 30/g' /etc/ssh/sshd_config \
&& sudo timedatectl set-timezone America/Los_Angeles
```

## Add repositories
- Add NVIDIA CUDA apt repository.
- Add Roche RSC-HTP apt repository.
- Add cmake apt repository.
- Install 1) build dependencies, 2) system tools, and 3) analysis tools.  
- Install and hold cmake. See [here](https://apt.kitware.com/) for more info.  
- Update ~/.bashrc.
```bash
export DISTRO=$(grep ^ID= /etc/os-release | sed "s/.*=//g")$(grep ^VERSION_ID= /etc/os-release | sed -e "s/\"//g" -e "s/.*=//g" -e "s/\.//g") \
&& export DISTRO_MAJOR=$(grep VERSION_ID /etc/os-release | sed -e "s/\"//g" -e "s/.*=//g" -e "s/\..*//g") \
&& wget -q -O - https://developer.download.nvidia.com/compute/cuda/repos/${DISTRO}/x86_64/3bf863cc.pub \
| gpg --dearmor \
| sudo tee /usr/share/keyrings/cuda.gpg > /dev/null \
&& echo "deb [signed-by=/usr/share/keyrings/cuda.gpg] http://developer.download.nvidia.com/compute/cuda/repos/${DISTRO}/x86_64/ /" \
| sudo tee /etc/apt/sources.list.d/cuda.list > /dev/null \
&& sudo apt update \
&& echo "export CMAKE_GENERATOR=Ninja" | tee -a ~/.bashrc > /dev/null \
&& echo "export PATH=/usr/local/cuda/bin:\$PATH" | tee -a ~/.bashrc > /dev/null
```

## Install build dependencies
```
sudo apt -yqq install ccache doxygen g++ gcc gdb git libboost-all-dev libbsd-dev libbz2-dev libnuma-dev libssl-dev libsystemd-dev libtbb-dev make ninja-build pre-commit zlib1g-dev \
&& sudo apt -yqq install ipmitool nfs-kernel-server nload nvtop rclone tmux tree \
&& sudo apt -yqq install hdf5-tools seqtk \
&& sudo apt -yqq autoremove \
&& sudo apt-mark hold cmake cmake-data
```

## Install NVIDIA driver
See [here](nvidia.md#install) for more info.
```bash
NVIDIA_VERSION=560.35.03-0ubuntu1 \
&& NVIDIA_MAJOR=$(echo ${NVIDIA_VERSION} | sed -e "s/\..*//g") \
&& sudo apt-mark unhold nvidia-driver-${NVIDIA_MAJOR}-open \
&& sudo apt -yqq --allow-downgrades install \
    libnvidia-cfg1-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-compute-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-decode-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-encode-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-extra-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-fbc1-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    libnvidia-gl-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    nvidia-compute-utils-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    nvidia-dkms-${NVIDIA_MAJOR}-open=${NVIDIA_VERSION} \
    nvidia-driver-${NVIDIA_MAJOR}-open=${NVIDIA_VERSION} \
    nvidia-kernel-common-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    nvidia-kernel-open-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    nvidia-kernel-source-${NVIDIA_MAJOR}-open=${NVIDIA_VERSION} \
    nvidia-utils-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
    xserver-xorg-video-nvidia-${NVIDIA_MAJOR}=${NVIDIA_VERSION} \
&& sudo apt-mark hold nvidia-driver-${NVIDIA_MAJOR}-open
```

## Install CUDA, CuDNN, NvInfer
```bash
CUDA_VERSION=12-8 \
&& CUDNN_VERSION=9.7.0.66-1 \
&& NVINFER_VERSION=10.8.0.43-1+cuda12.8 \
&& CUDA_MAJOR=$(echo ${CUDA_VERSION} | sed -e "s/-.*//g") \
&& CUDNN_MAJOR=$(echo ${CUDNN_VERSION} | sed -e "s/\..*//g") \
&& NVINFER_MAJOR=$(echo ${NVINFER_VERSION} | sed -e "s/\..*//g") \
&& sudo apt-mark unhold \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR} \
    libcudnn${CUDNN_MAJOR}-dev-cuda-${CUDA_MAJOR} \
    libnvinfer${NVINFER_MAJOR} \
    libnvinfer-dev \
    libnvinfer-headers-dev \
&& sudo apt -yqq --allow-downgrades install \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
&& sudo apt -yqq --allow-downgrades install \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR}=${CUDNN_VERSION} \
    libcudnn${CUDNN_MAJOR}-dev-cuda-${CUDA_MAJOR}=${CUDNN_VERSION} \
&& sudo apt -yqq --allow-downgrades install \
    libnvinfer${NVINFER_MAJOR}=${NVINFER_VERSION} \
    libnvinfer-dev=${NVINFER_VERSION} \
    libnvinfer-headers-dev=${NVINFER_VERSION} \
&& sudo apt-mark hold \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR} \
    libcudnn${CUDNN_MAJOR}-dev-cuda-${CUDA_MAJOR} \
    libnvinfer${NVINFER_MAJOR} \
    libnvinfer-dev \
    libnvinfer-headers-dev \
&& sudo apt -yqq install nvidia-docker2
```
If installing without dev packages.
```bash
CUDA_VERSION=12-8 \
&& CUDNN_VERSION=9.7.0.66-1 \
&& NVINFER_VERSION=10.8.0.43-1+cuda12.8 \
&& CUDA_MAJOR=$(echo ${CUDA_VERSION} | sed -e "s/-.*//g") \
&& CUDNN_MAJOR=$(echo ${CUDNN_VERSION} | sed -e "s/\..*//g") \
&& NVINFER_MAJOR=$(echo ${NVINFER_VERSION} | sed -e "s/\..*//g") \
&& sudo apt-mark unhold \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR} \
    libnvinfer${NVINFER_MAJOR} \
&& sudo apt -yqq --allow-downgrades install \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR}=${CUDNN_VERSION} \
    libnvinfer${NVINFER_MAJOR}=${NVINFER_VERSION} \
&& sudo apt-mark hold \
    cuda-toolkit-${CUDA_VERSION} \
    nvidia-gds-${CUDA_VERSION} \
    libcudnn${CUDNN_MAJOR}-cuda-${CUDA_MAJOR} \
    libnvinfer${NVINFER_MAJOR}
```

## IPMI (optional)
Tested only for Supermicro and Advantech motherboards.
Set IPMI user and IP static.  
See [here](ipmi.md#set-user-and-password) for more info.
```bash
sudo ipmitool user list 1 \
&& sudo ipmitool user set name 3 genia \
&& sudo ipmitool user set password 3 <password> \
&& sudo ipmitool user enable 3 \
&& sudo ipmitool user priv 3 0x4 1 \
&& sudo ipmitool user list 1 \
&& sudo ipmitool lan set 1 ipsrc static
```

## Check installed dependencies
```bash
apt list --installed | egrep "nvidia-driver|cuda-toolkit|libcudnn|libnvinfer|htp-nsa"
```

## Install python
Please see [python.md](python.md).

## Reboot
```bash
sudo reboot
```
