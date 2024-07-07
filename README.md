# Debian 12 + PREEMPT_RT + Etherlab EtherCAT Master

> [!NOTE]  
> Procedure Has Been Successfully Tested With:
> 
> Debian 12 (Bookworm) Point Release 12.6 (29 June 2024)
> 
> Kernel Version 6.1.96 (27 June 2024)
> 
> Real Time Patch Version 6.1.96-RT35 (03 July 2024)

#### Step 01 >>> Install Required Packages
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
#### Step 02 >>> Retrieve Kernel Source Code
##### Download Kernel Source Code Archive
> [!IMPORTANT]  
> Selected Kernel Version Must Match Real Time Patch Version
```console
sudo wget -P /usr/src/ https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.96.tar.xz
```

##### Decompress Kernel Source Code Archive
```console
sudo tar -xvJf /usr/src/linux-6.1.96.tar.xz -C /usr/src
```
#### Step 03: Retrieve Real Time Patch
##### Download Real Time Patch Archive Into Kernel Source Code Directory
```console
sudo wget -P /usr/src/linux-6.1.96/ https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.96-rt35.patch.xz
```
* Selected Real Time Patch Version Must Match Kernel Version
#### Decompress Real Time Patch Archive Into Kernel Source Code Directory
```console
sudo xz -d /usr/src/linux-6.1.96/patch-6.1.96-rt35.patch.xz
```
### Step 04: Apply Real Time Patch
```console
cd /usr/src/linux-6.1.96 && sudo patch -p1 < patch-6.1.96-rt35.patch
```

### Step 05: Configure Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make menuconfig
```
* General Setup >>> Preemption Model >>> Fully Preemptible Kernel (Real-Time) >>> Checked
* Processor Type and Features >>> Timer Frequency >>> 1000 Hz >>> Checked
* Save & Exit

### Step 06: Build Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make -j$(nproc)
```

### Step 07: Install Kernel Modules
```console
cd /usr/src/linux-6.1.96 && sudo make modules_install
```

### Step 08: Install Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make install
```

### Step 09: Reboot System
```console
sudo reboot
```

### Step 10: Verify Active Kernel Version
```console
uname -a
```

# (Optional) Resolve Kernel Panic

* strip unneeded symbols of object files
```console
cd /lib/modules/6.1.96-rt35
```
```console
sudo find . -name *.ko -exec strip --strip-unneeded {} +
```
* change the compression format
```console
sudo vi /etc/initramfs-tools/initramfs.conf
```

change COMPRESS=lz4 to COMPRESS=xz

COMPRESS=xz 

[:wq]

* update initramfs

```console
sudo update-initramfs -u
```

* verify boot directory
* config-6.1.96-rt35
* initrd.img-6.1.96-rt35
* vmlinuz-6.1.96-rt35

* update grub

```console
sudo update-grub
```

* reboot

```console
sudo reboot
```

* verify

```console
uname -a
```

# Install Ethercat Master for SYSTEMD Distributions

#### Clone Github Repository
```console
cd ~ && git clone https://gitlab.com/etherlab.org/ethercat.git
```

#### Checkout Latest Stable Branch
```console
 cd ~/ethercat/ && git checkout stable-1.6
```

#### Run Bootstrap Script
```console
cd ~/ethercat/ && sudo ./bootstrap
```

#### Configure Ethercat Master
```console
cd ~/ethercat/ && sudo ./configure --enable-8139too=no --prefix=/opt/xethercatx
```

#### Build Ethercat Master
```console
sudo make all modules
```

#### Install Ethercat Master
```console
sudo make install
```
```console
sudo make modules_install
```
```console
sudo depmod
```
#### Identify Relevant Network Interface MAC Address

```console
ip addr show
```
* Copy Relevant MAC Address CTRL + SHIFT + C

#### Modify ethercat.conf File
```console
sudo vi /opt/xethercatx/etc/ethercat.conf
```
* VIM Activate Editing Mode >>> i
* Navigate to MASTER0_DEVICE="" and paste MAC address CTRL + SHIFT + V
* Navigate to DEVICE_MODULES="" and paste driver keyword
* VIM Activate Normal Mode >>> Esc
* VIM Write & Quit >>> :wq + Return

#### Enable Service to Start Automatically at System Startup
```console
sudo systemctl enable ethercat.service
```
* Reboot in order to automatically start the service
```console
sudo reboot
```
* Verify if the service has automatically started
```console
sudo systemctl status ethercat.service
```

#### Change File Mode Permissions
```console
sudo chmod 666 /dev/EtherCAT0
```

# misc

```console
modprobe ec_master main_devices=30:2f:1e:29:45:85 queue=1
```
