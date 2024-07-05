# general tools: wiping disks

* show disks and partitions in order to identify the target disk

```console
sudo fdisk -l
```

* wipe target disk
  * dd command to writes zeroes to the first few megabytes of the disk, which is where the partition table and GPT metadata are stored.
  * if=/dev/zero: input file is a stream of zeroes.
  * of=/dev/sdX: output file is your disk.
  * bs=512: block size is 512 bytes.
  * count=2048: number of blocks to write. 2048 blocks of 512 bytes each will cover the first 1 MB of the disk, which includes the primary GPT header and partition table, as well as the protective MBR.
  * replace /dev/sdX with the specific disk identifier (e.g., /dev/sda).

```console
sudo dd if=/dev/zero of=/dev/sdX bs=512 count=2048
```

# install ethercat master setup from scratch on debian operating system

* update and upgrade the system

```console
sudo apt update
```

```console
sudo apt upgrade
```

* install necessary packages
```console
sudo apt install build-essential libncurses-dev bison flex libssl-dev libelf-dev bc
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
sudo patch -p1 < patch-6.1.96-rt35.patch.xz
```

* configure kernel
 * general setup >>> preemption model >>> fully preemptible kernel (real-time)
 * processor type and features >>> timer frequency >>> 1000 Hz
 * save & exit
```console
sudo make menuconfig
```


# incomplete

```console
cd
```

```console
sudo apt update
```

```console
sudo apt install autoconf
```

```console
sudo apt install automake
```

```console
sudo apt install libtool
```

```console
sudo apt install pkg-config
```

```console
mkdir xecatbuildx
```

```console
cd xecatbuildx
```

```console
cd ethercat
```

```console
sudo ./bootstrap
```

```console
cd
```

```console
sudo mv xecatbuildx /usr/local/src/
```

```console
sudo ln -s /usr/local/src/xecatbuildx/ethercat ~/ethercat
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
