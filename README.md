# Install Required Packages
```console
sudo apt update
```
```console
sudo apt upgrade -y
```
```console
sudo apt install autoconf automake bc bison build-essential dwarves flex git libelf-dev libncurses-dev libssl-dev libtool mlocate pkg-config udev vim wget -y
```
```console
sudo reboot
```

# Install Real Time Patch

#### Download Kernel Source Code Archive
```console
sudo wget -P /usr/src/ https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.96.tar.xz
```
* Select Kernel Version That Has a Matching Real Time Patch Version
* Kernels: https://cdn.kernel.org/pub/linux/kernel/

#### Decompress Kernel Source Code Archive
```console
sudo tar -xvf /usr/src/linux-6.1.96.tar.xz
```

#### Download Real Time Patch Archive Into Kernel Source Code Directory
```console
sudo wget -P /usr/src/linux-6.1.96/ https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.96-rt35.patch.xz
```
* Select Real Time Patch Version That Matches the Kernel Version
* Patches: https://cdn.kernel.org/pub/linux/kernel/projects/rt/

#### Decompress Real Time Patch Archive
```console
sudo xz -d /usr/src/linux-6.1.96/patch-6.1.96-rt35.patch.xz
```

#### Apply Real Time Patch
```console
cd /usr/src/linux-6.1.96 && sudo patch -p1 < patch-6.1.96-rt35.patch
```
#### Configure Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make menuconfig
```
* General Setup >>> Preemption Model >>> Fully Preemptible Kernel (Real-Time)
* Processor Type and Features >>> Timer Frequency >>> 1000 Hz
* Save & Exit

#### Build Kernel Using All Available CPU Cores
```console
cd /usr/src/linux-6.1.96 && sudo make -j$(nproc)
```

#### Install Kernel Modules
```console
cd /usr/src/linux-6.1.96 && sudo make modules_install
```

#### Install Kernel
```console
cd /usr/src/linux-6.1.96 && sudo make install
```

#### Reboot System
```console
sudo reboot
```

#### Verify Active Kernel Version
```console
uname -a
```

# Resolve Kernel Panic (Optional)

```console
cd /lib/modules/6.1.96-rt35
```
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

#### Enable Service to Start Automatically at Boot Time
```console
sudo systemctl enable ethercat.service
```





#### Create Symbolic Link
```console
sudo ln -s /opt/xethercatx/etc/init.d/ethercat /etc/init.d/
```

#### Copy Sysconfig File
```console
sudo cp /opt/xethercatx/etc/ethercat.conf /etc/
```

#### Start Ethercat Master

```console
sudo /opt/xethercatx/etc/init.d/ethercat start
```
```console
sudo ln -s /opt/xethercatx/etc/init.d/ethercat /etc/init.d/
```

## verify ethercat installation

```console
sudo systemctl status ethercat.service
```

```console
sudo systemctl enable ethercat.service
```

```console
sudo systemctl start ethercat.service
```

```console
sudo systemctl status ethercat.service
```

```console
modprobe ec_master main_devices=30:2f:1e:29:45:85 queue=1
```
