# Raspberry Pi + ROS2 + Hailo (AI Kit or AI HAT+)
Raspberry Pi 5 with AI Hat (Hailo 8L, 13 TOPS) in ROS2

## Requirements
Please install the latest Ubuntu LTS version available from the RPi-Imager.

## Reference
[Official Installation Steps for HailoRT](https://hailo.ai/developer-zone/documentation/hailort-v4-20-0/?sp_referrer=install/install.html#ubuntu-installer-requirements)

## Installation Steps
### HailoRT + PCIe Driver
- Go to the official [Software Downloads website](https://hailo.ai/developer-zone/software-downloads/).
- Download "HailoRT – PCIe driver Ubuntu package (deb)" and "HailoRT – Ubuntu package (deb) for arm64".
- Run
  ```
  sudo dpkg --install hailort_<version>_$(dpkg --print-architecture).deb hailort-pcie-driver_<version>_all.deb
  ```
  (Note: A prompt regarding using DKMS (Dynamic Kernel Module Support) is expected and recommended to be approved.)

- Reboot your Raspberry Pi.
- Please run
  ```
  hailortcli fw-control identify
  ```
  to verify the installation. You should have an output something like
  ```
  Executing on device: xxxx:xx:xx.x
  Identifying board
  Control Protocol Version: 2
  Firmware Version: 4.20.0 (release,app,extended context switch buffer)
  Logger Version: 0
  Board Name: Hailo-8
  Device Architecture: HAILO8L
  Serial Number: <N/A>
  Part Number: <N/A>
  Product Name: <N/A>
  ```
  It is normal to have <N/A>s printed in the last three components.

### ROS2 Setup
#### Installation
- Please follow the [official installation steps for Jazzy](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html) (ROS2 for Ubuntu 24.04 LTS).
- Initialize ROS2
```
source /opt/ros/jazzy/setup.bash
```
- Create a workspace and clone this repo:
```
mkdir -p ~/pi_hailo_ros2_ws/src
cd ~/pi_hailo_ros2_ws/src/
git clone https://github.com/koh43/pi_hailo_ros2.git
```
- Create a Python virtual environment for this ROS2 package and for installing TAPPAS
```
sudo apt install python3-venv
cd ~/pi_hailo_ros2_ws/
python3 -m venv --system-site-packages ./pi_hailo_ros2_venv
source ~/pi_hailo_ros2_ws/pi_hailo_ros2_venv/bin/activate
# For ROS2 builds
touch ~/pi_hailo_ros2_ws/pi_hailo_ros2_venv/COLCON_IGNORE
```
- Build the ROS2 package:
```
sudo apt install python3-colcon-common-extensions
colcon build --symlink-install
source ~/pi_hailo_ros2_ws/install/local_setup.bash
```

### TAPPAS
#### Setup Python Virtual Environment
```
cd ~
source ~/pi_hailo_ros2_ws/pi_hailo_ros2_venv/bin/activate
```
- Go to the official [Software Downloads website](https://hailo.ai/developer-zone/software-downloads/).
- Download " TAPPAS – Linux installer".
- Here is the [official instruction](https://github.com/hailo-ai/tappas/blob/master/docs/installation/manual-install.rst).
- Before you go through the installation steps, please modify the following:
    - Under tappas_<your_tappas_v>/core/requirements/gstreamer_requirements.txt, change **numpy** version to **1.26.4**.
    - Under tappas_<your_tappas_v>/core/requirements/requirements.txt, remove the versions (==x.x.x) specified for **ninja** and **meson**.
    - Under tappas_<your_tappas_v>/downloader/requirements.txt, remove the versions (==x.x.x) specified for **requests** and **boto3**.
    - Under tappas_<your_tappas_v>/tools/run_app, make a copy of requirements_22_04.txt and name it **requirements_24_04.txt**.

#### Verification
Try to run
```
gst-inspect-1.0 hailotools
```
You should have an output like
```
Plugin Details:
  Name                     hailotools
  Description              hailo tools plugin
  Filename                 /opt/hailo/tappas/lib/aarch64-linux-gnu/gstreamer-1.0/libgsthailotools.so
  Version                  3.31.0
  License                  unknown
  Source module            gst-hailo-tools
  Binary package           gst-hailo-tools
  Origin URL               https://hailo.ai/

  hailoaggregator: hailoaggregator - Cascading
  hailocounter: hailocounter - postprocessing element
  hailocropper: hailocropper
  hailoexportfile: hailoexportfile - export element
  hailoexportzmq: hailoexportzmq - export element
  hailofilter: hailofilter - postprocessing element
  hailogallery: Hailo gallery element
  hailograytonv12: hailograytonv12 - postprocessing element
  hailoimportzmq: hailoimportzmq - import element
  hailomuxer: Muxer pipeline merging
  hailonv12togray: hailonv12togray - postprocessing element
  hailonvalve: HailoNValve element
  hailooverlay: hailooverlay - overlay element
  hailoroundrobin: Input Round Robin element
  hailostreamrouter: Hailo Stream Router
  hailotileaggregator: hailotileaggregator
  hailotilecropper: hailotilecropper - Tiling
  hailotracker: Hailo object tracking element

  18 features:
  +-- 18 elements
```

## Installation Issues
If you have the following error when installing HailoRT,
```
/share/opt/hailo/linux/pcie /
make[1]: *** /lib/modules/6.8.0-1020-raspi//build: No such file or directory.  Stop.
make: *** [Makefile:100: clean] Error 2
Failed. Exited with status 2. See /var/log/hailort-pcie-driver.deb.log
Failed at 50
   47	}
   48	
   49	function compile_and_install_pcie_driver_without_dkms() {
   50	    make clean &>> $LOG
   51	    make all &>> $LOG
   52	    make install &>> $LOG
   53	}
```
try to run the following code to install the right kernel version for HailoRT
```
sudo apt install linux-headers-$(uname -r)
```
