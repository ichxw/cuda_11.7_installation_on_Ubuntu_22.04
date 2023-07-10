# cuda_11.7_installation_on_Ubuntu_22.04
* The Linux system I'm working on is Ubuntu 22.04.2 LTS. To install a nvidia driver version of 515 and cuda 11.7, I followed this [instruction](https://gist.github.com/X-TRON404/e9cab789041ef03bcba13da1d5176e28).
* However, it alwasy gave me message as follows after installation.
  ```
  nvidia-smi
  ```
  ```
  | NVIDIA-SMI 535.54.03 Driver Version: 535.54.03 CUDA Version: 12.2 |
  ```
* Command nvcc -V could show the correct version of cuda 11.7
  ```
  nvcc -V
  nvcc -V shows the correct version:
  nvcc: NVIDIA (R) Cuda compiler driver
  Copyright (c) 2005-2022 NVIDIA Corporation
  Built on Wed_Jun__8_16:49:14_PDT_2022
  Cuda compilation tools, release 11.7, V11.7.99
  Build cuda_11.7.r11.7/compiler.31442593_0
  ```
* However, pytorch (with cuda 11.7) can't detect the GPU with a return of False for this command torch.cuda.is_available()
* After working on this a little bit, I finally identified the problem. It was this command in the instruction that uninstall replace nvidia driver 515 with 535 for no reason. 
  ```
  sudo apt install cuda-11-7

  (base) software$ sudo apt install cuda-11-7
  Reading package lists... Done
  Building dependency tree... Done
  Reading state information... Done
  The following package was automatically installed and is no longer required:
    libnvidia-common-515
  Use 'sudo apt autoremove' to remove it.
  The following additional packages will be installed:
    cuda-demo-suite-11-7 cuda-drivers cuda-drivers-535 cuda-runtime-11-7 cuda-toolkit-11-7 libnvidia-cfg1-535 libnvidia-common-535 libnvidia-compute-535 libnvidia-decode-535 libnvidia-encode-535 libnvidia-extra-535 libnvidia-fbc1-535
    libnvidia-gl-535 nvidia-compute-utils-535 nvidia-dkms-535 nvidia-driver-535 nvidia-kernel-common-535 nvidia-kernel-source-535 nvidia-modprobe nvidia-utils-535 xserver-xorg-video-nvidia-535
  Recommended packages:
    libnvidia-compute-535:i386 libnvidia-decode-535:i386 libnvidia-encode-535:i386 libnvidia-fbc1-535:i386 libnvidia-gl-535:i386
  The following packages will be REMOVED:
    libnvidia-cfg1-515 libnvidia-compute-515 libnvidia-decode-515 libnvidia-encode-515 libnvidia-extra-515 libnvidia-fbc1-515 libnvidia-gl-515 nvidia-compute-utils-515 nvidia-dkms-515 nvidia-driver-515 nvidia-kernel-common-515
    nvidia-kernel-source-515 nvidia-utils-515 xserver-xorg-video-nvidia-515
  The following NEW packages will be installed:
    cuda-11-7 cuda-demo-suite-11-7 cuda-drivers cuda-drivers-535 cuda-runtime-11-7 libnvidia-cfg1-535 libnvidia-common-535 libnvidia-compute-535 libnvidia-decode-535 libnvidia-encode-535 libnvidia-extra-535 libnvidia-fbc1-535 libnvidia-gl-535
    nvidia-compute-utils-535 nvidia-dkms-535 nvidia-driver-535 nvidia-kernel-common-535 nvidia-kernel-source-535 nvidia-modprobe nvidia-utils-535 xserver-xorg-video-nvidia-535
  ```
A workaround is to follow this [instruction](https://gist.github.com/X-TRON404/e9cab789041ef03bcba13da1d5176e28) before sudo apt install cuda-11-7. 
  ```
  * #!/bin/bash
  
  lspci | grep -i nvidia
  
  * If you have previous installation remove it first. 
  sudo apt-get purge nvidia*
  sudo apt remove nvidia-*
  sudo rm /etc/apt/sources.list.d/cuda*
  sudo apt-get autoremove && sudo apt-get autoclean
  sudo rm -rf /usr/local/cuda*
  
  * system update
  sudo apt-get update
  sudo apt-get upgrade
  
  * install other import packages
  sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
  
  * first get the PPA repository driver
  sudo add-apt-repository ppa:graphics-drivers/ppa
  sudo apt update
  
  * install nvidia driver with dependencies
  sudo apt install libnvidia-common-515
  sudo apt install libnvidia-gl-515
  sudo apt install nvidia-driver-515
  
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
  sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
  sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/3bf863cc.pub
  sudo add-apt-repository "deb https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/ /"
  sudo apt-get update
  ```
The following steps are to download necessary packages from https://developer.download.nvidia.com/compute/cuda/repos/$distro/$arch and install them one by one. Here my $distro/$arch is ubuntu2204/x86_64. 
  ```
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-keyring_1.1-1_all.deb
  sudo dpkg -i cuda-keyring_1.1-1_all.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-toolkit-11-7_11.7.0-1_amd64.deb
  sudo dpkg -i cuda-toolkit-11-7_11.7.0-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-nvml-dev-11-7_11.7.91-1_amd64.deb
  sudo dpkg -i cuda-nvml-dev-11-7_11.7.91-1_amd64.deb
  sudo dpkg -i cuda-toolkit-11-7_11.7.0-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-documentation-11-7_11.7.91-1_amd64.deb
  sudo dpkg -i cuda-documentation-11-7_11.7.91-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-tools-11-7_11.7.1-1_amd64.deb
  sudo dpkg -i cuda-tools-11-7_11.7.1-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/gds-tools-11-7_1.3.1.18-1_amd64.deb
  sudo dpkg -i gds-tools-11-7_1.3.1.18-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-visual-tools-11-7_11.7.1-1_amd64.deb
  sudo dpkg -i cuda-visual-tools-11-7_11.7.1-1_amd64.deb
  wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-nvvp-11-7_11.7.101-1_amd64.deb
  sudo dpkg -i cuda-nvvp-11-7_11.7.101-1_amd64.deb
  sudo add-apt-repository ppa:openjdk-r/ppa
  sudo apt-get update
  sudo apt-get install openjdk-8-jre
  sudo dpkg -i cuda-nvvp-11-7_11.7.101-1_amd64.deb
  sudo dpkg -i cuda-tools-11-7_11.7.1-1_amd64.deb
  sudo dpkg -i cuda-keyring_1.1-1_all.deb
  sudo dpkg -i cuda-toolkit-11-7_11.7.0-1_amd64.deb
  sudo reboot
  ```
* The tip is to follow the error message and download whatever is missing from https://developer.download.nvidia.com/compute/cuda/repos/$distro/$arch and install it.
* After all those steps, I finally got the right nvidia driver and cuda on Ubuntu:
  ```
  (base) software$ nvidia-smi
  +-----------------------------------------------------------------------------+
  | NVIDIA-SMI 515.105.01   Driver Version: 515.105.01   CUDA Version: 11.7     |
  ```
* pytorch can find the GPUs and other software which uses GPU also worked.
* 


