# ethercat-master-setup

* debian version 12.6

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
