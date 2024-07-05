# wiping disks (optional)

* show disks and partitions in order to identify the target disk identifier

```console
sudo fdisk -l
```

* wipe target disk
  * dd command to writes zeroes to the first few megabytes of the disk, which is where the partition table and GPT metadata are stored.
  * if=/dev/zero: input file is a stream of zeroes.
  * of=/dev/XXX: output file is your disk.
  * bs=512: block size is 512 bytes.
  * count=2048: number of blocks to write. 2048 blocks of 512 bytes each will cover the first 1 MB of the disk, which includes the primary GPT header and partition table, as well as the protective MBR.
  * replace /dev/XXX with the specific disk identifier.

```console
sudo dd if=/dev/zero of=/dev/XXX bs=512 count=2048
```

# real time patch setup

* step 01: update the system

```console
sudo apt update
```

* step 02: upgrade the system
```console
sudo apt upgrade -y
```

* step 03: install required packages
```console
sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev bc wget dwarves -y
```

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

# ethercat master setup

* step 01: navigate into home directory

```console
cd ~
```

```console
sudo apt update
```

* install required packages

```console
sudo apt install autoconf automake libtool pkg-config git -y
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

* configure ethercat master setup

```console
sudo ./configure --enable-8139too=no
```

* run make

```console
sudo make
```

```console
sudo make modules
```

```console
sudo make install
```

```console
sudo make modules_install
```

## install ethercat

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
