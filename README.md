# Installing EtherLab EtherCAT Master + Debian 12 + PREEMPT_RT Patch
> [!NOTE]
> Procedure Has Been Successfully Tested With:
> 
> Debian 12 (Bookworm) Point Release 12.6 (29 June 2024)
> 
> Kernel Version 6.1.96 (27 June 2024)
> 
> Real Time Patch Version 6.1.96-RT35 (03 July 2024)
#### Step 01 | Install Required Packages
```console
sudo apt update
```
```console
sudo apt upgrade -y
```
```console
sudo apt install autoconf automake bc bison build-essential dwarves flex git libelf-dev libncurses-dev libssl-dev libtool pkg-config udev vim wget -y
```
```console
sudo reboot
```
#### Step 02 | Retrieve Kernel Source Code
##### Download Kernel Source Code Archive
> [!IMPORTANT]  
> The Selected Kernel Version Must Match the Real-Time Patch Version
```console
sudo wget -P /usr/src/ https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.96.tar.xz
```
##### Decompress Kernel Source Code Archive
```console
sudo tar -xvJf /usr/src/linux-6.1.96.tar.xz -C /usr/src
```
#### Step 03: Retrieve Real Time Patch
##### Download Real Time Patch Archive Into Kernel Source Code Directory
> [!IMPORTANT]  
> The Selected Real-Time Patch Version Must Match the Kernel Version
```console
```console
sudo wget -P /usr/src/linux-6.1.96/ https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.96-rt35.patch.xz
```
##### Decompress Real Time Patch Archive Into Kernel Source Code Directory
```console
sudo xz -d /usr/src/linux-6.1.96/patch-6.1.96-rt35.patch.xz
```
#### Step 04 | Apply Real Time Patch
```console
cd /usr/src/linux-6.1.96 && sudo patch -p1 < patch-6.1.96-rt35.patch
```
#### Step 05 | Configure Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make menuconfig
```
* General Setup >>> Preemption Model >>> Fully Preemptible Kernel (Real-Time) >>> Checked
* Processor Type and Features >>> Timer Frequency >>> 1000 Hz >>> Checked
* Save & Exit
#### Step 06 | Build Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make -j$(nproc)
```
#### Step 07 | Install Kernel Modules
```console
cd /usr/src/linux-6.1.96 && sudo make modules_install
```
#### Step 08 | Install Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make install
```
#### Step 09 | Reboot System
```console
sudo reboot
```
#### Step 10 | Verify Active Kernel Version
```console
uname -a
```
> [!CAUTION]
> Resolve Kernel Panic (Optional)
> 
> Step 11 to Step 16 Are Optional in Case the Reboot in Step 09 Causes a Kernel Panic
#### Step 11 | Remove Unnecessary Symbols From Object Files
```console
cd /lib/modules/6.1.96-rt35 && sudo find . -name *.ko -exec strip --strip-unneeded {} +
```
#### Step 12 | Change the Compression Format
```console
sudo vi /etc/initramfs-tools/initramfs.conf
```
##### Change Compression Algorithm From ZSTD to XZ
* VIM Activate Editing Mode >>> i
* COMPRESS=xz
* VIM Activate Normal Mode >>> Esc
* VIM Write & Quit >>> :wq + Return
#### Step 13 | Update the Initial Ram Filesystem for the Current Kernel Version
```console
sudo update-initramfs -u
```
#### Step 14 | Update Grub (Grand Unified Bootloader) Configuration Files
```console
sudo update-grub
```
#### Step 15 | Reboot System
```console
sudo reboot
```
#### Step 16 | Verify Active Kernel Version
```console
uname -a
```
> [!WARNING]
> The official EtherLab documentation for installing the EtherCAT master is tailored to Linux distributions that use the traditional `System V` initialization system based on modifications to `/etc/init.d/` and `/etc/sysconfig/`. Therefore, it is not applicable to Linux distributions such as the latest Debian 12 (Bookworm) release, which relies on the modern `systemd` initialization system and service manager. Consequently, this manual outlines the necessary steps to install the EtherLab EtherCAT master on Debian 12 (Bookworm) using the `systemd` initialization system and service manager.

#### Step 17 | Clone Etherlab Ethercat Master Github Repository
```console
cd ~ && git clone https://gitlab.com/etherlab.org/ethercat.git
```
#### Step 18 | Checkout Latest Stable Branch
```console
 cd ~/ethercat/ && git checkout stable-1.6
```
#### Step 19 | Run Bootstrap Script
```console
cd ~/ethercat/ && sudo ./bootstrap
```
#### Step 20 | Configure Ethercat Master
```console
cd ~/ethercat/ && sudo ./configure --enable-8139too=no --prefix=/opt/etherlab
```
#### Step 21 | Build Ethercat Master
```console
cd ~/ethercat/ && sudo make all modules
```
#### Step 22 | Install Ethercat Master
```console
cd ~/ethercat/ && sudo make install
```
```console
cd ~/ethercat/ && sudo make modules_install
```
```console
cd ~/ethercat/ && sudo depmod
```
#### Step 23 | Identify Relevant Network Interface MAC Address
```console
ip addr show
```
##### Copy Relevant MAC Address (CTRL + SHIFT + C)
#### Step 24 | Modify ethercat.conf File
```console
sudo vi /opt/etherlab/etc/ethercat.conf
```
* VIM Activate Editing Mode >>> i
* MASTER0_DEVICE="aa:bb:cc:dd:ee:ff" (CTRL + SHIFT + V)
* DEVICE_MODULES="generic"
* VIM Activate Normal Mode >>> Esc
* VIM Write & Quit >>> :wq + Return

#### Step 25 | Change File Mode Permissions
```console
sudo chmod 666 /dev/EtherCAT0
```

#### Step 26 | Enable Service to Start Automatically at System Startup
```console
sudo systemctl enable ethercat.service
```

#### Step 27 | Reboot in Order to Automatically Start the Service
```console
sudo reboot
```

#### Step 28 | Verify if the Service Is Enabled and Active
```console
sudo systemctl status ethercat.service
```

#### Optional Library Integration Using CMake
```cmake
# CMakeLists.txt

# Add the path to the ethercat include directory
include_directories(/opt/etherlab/include)

# Add the path to the ethercat library directory
link_directories(/opt/etherlab/lib)

# Find and link the ethercat library
find_library(ETHERCAT_LIBRARY NAMES ethercat PATHS /opt/etherlab/lib)

if(NOT ETHERCAT_LIBRARY)
    message(FATAL_ERROR "Ethercat Library Has Not Been Found")
endif()
```

# misc
```console
modprobe ec_master main_devices=30:2f:1e:29:45:85 queue=1
```
