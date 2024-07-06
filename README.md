

# Install Required Packages

```console
sudo apt update
```

```console
sudo apt upgrade -y
```

```console
sudo apt install autoconf automake bc bison build-essential dwarves flex git libelf-dev libncurses-dev libssl-dev libtool mlocate pkg-config wget -y
```

# Install Real Time Patch

* navigate to download directory
```console
cd /usr/src
```

* download kernel source code archive
  * select kernel version that has a matching real time patch version
  * kernels: https://cdn.kernel.org/pub/linux/kernel/

```console
sudo wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.1.96.tar.xz
```

* decompress kernel source code archive

```console
sudo tar -xvf linux-6.1.96.tar.xz
```

* navigate into kernel source code directory

```console
cd linux-6.1.96
```

* download real time patch archive into kernel source code directory
  * select real time patch version that matches the kernel version
  * patches: https://cdn.kernel.org/pub/linux/kernel/projects/rt/

```console
sudo wget https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.1/patch-6.1.96-rt35.patch.xz
```

* decompress real time patch
```console
sudo xz -d patch-6.1.96-rt35.patch.xz
```

* apply real time patch
```console
sudo patch -p1 < patch-6.1.96-rt35.patch
```

* configure kernel
  * general setup >>> preemption model >>> fully preemptible kernel (real-time)
  * processor type and features >>> timer frequency >>> 1000 Hz
  * save & exit

```console
sudo make menuconfig
```

* compile kernel using all available CPU cores
```console
sudo make -j$(nproc)
```

* install kernel modules
```console
sudo make modules_install
```

* install compiled kernel 
```console
sudo make install
```

* reboot system
```console
sudo reboot
```

* verify kernel version

```console
uname -a
```

# Optional: Kernel Panic Management

* install vim
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
sudo apt install vim
```

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















# Install Ethercat Master

* navigate into home directory

```console
cd ~
```

* clone github repository

```console
git clone https://gitlab.com/etherlab.org/ethercat.git
```

* navigate into ethercat directory

```console
cd ethercat
```

* checkout latest stable branch

```console
 git checkout stable-1.6
```

* run bootstrap script

```console
sudo ./bootstrap
```

* Configure Ethercat Master

```console
sudo ./configure --enable-8139too=no --prefix=/opt/etherlab
```

* Build Ethercat Master

```console
sudo make all modules
```

* Install Ethercat Master

```console
sudo make install
```
```console
sudo make modules_install
```
* Identify Relevant Network Interface MAC Address

```console
ip addr show
```

## verify ethercat installation

```console
systemctl status ethercat.service
```

```console
sudo systemctl enable ethercat.service
```

```console
sudo systemctl start ethercat.service
```
```console
modprobe ec_master main_devices=30:2f:1e:29:45:85 queue=1
```
