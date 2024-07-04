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

# install ethercat master setup from scratch on debian version 12.6.0

* tested with debian version 12.6.0
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
