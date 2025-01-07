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
- Run
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

### TAPPAS
#### Setup Python Virtual Environment (venv, just for compiling TAPPAS)
```
mkdir -p venv
python3 -m venv --system-site-packages venv/tappas
source venv/tappas/bin/activate
```
- Go to the official [Software Downloads website](https://hailo.ai/developer-zone/software-downloads/).
- Download " TAPPAS – Linux installer".
- Here is the [official instruction](https://github.com/hailo-ai/tappas/blob/master/docs/installation/manual-install.rst).
- Before you go through the installation steps, please modify the following:
    - Under tappas_<your_tappas_v>/core/requirements/gstreamer_requirements.txt, change **numpy** version to **1.26.4**.
    - Under tappas_<your_tappas_v>/core/requirements/requirements.txt, remove the versions (==x.x.x) specified for **ninja** and **meson**.
    - Under tappas_<your_tappas_v>/downloader/requirements.txt, remove the versions (==x.x.x) specified for **requests** and **boto3**.
    - Under tappas_<your_tappas_v>/tools/run_app, make a copy of requirements_22_04.txt and name it **requirements_24_04.txt**.
