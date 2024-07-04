# ethercat-master-setup

## PREEMPT_RT patch installation

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
