---
layout: post
title: "Ubuntu Server Configuration"
published: true
categories: [server]
---

### Configure Static IP Address
The default configuration files of Netplan are found under `/etc/netplan/` directory.
```shell
$ ls /etc/netplan/
50-cloud-init.yaml
```
The default network configuration file is `50-cloud-init.yaml`.

```shell
$ cat /etc/netplan/50-cloud-init.yaml
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    ethernets:
        eth0:
            dhcp4: no
            addresses: [10.10.10.212/24]
            gateway4: 10.10.10.20
            nameservers:
                addresses: [8.8.4.4,8.8.8.8]
```

#### Apply configuration
```shell
$ sudo netplan --debug apply
```
**netplan apply** applies the current netplan configuration to a running system.

**–debug** Print debugging output during the process.

### Configure Bridge interface
Install the bridge-utils
```shell
$ sudo apt install bridge-utils
```

Configuration files of Netplan in `/etc/netplan/01-netcfg.yaml`.
* set bridge interface on `eno1`
* ip address `10.10.20.12/24`
* gateway ipv4 `10.10.10.20`

```shell
$ ls /etc/netplan
01-netcfg.yaml  01-netcfg.yaml.default  01-netcfg.yaml.default.bridges

# show config in `01-netcfg.yaml`
$ cat /etc/netplan/01-netcfg.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eno1:
      dhcp4: no
    eno2:
      dhcp4: no
      addresses: [10.10.20.12/24]
  bridges:
    br0:
      dhcp4: no        
      addresses: [10.10.10.12/24]
      gateway4: 10.10.10.20
      nameservers:
        addresses: [8.8.4.4,8.8.8.8]
      interfaces:
      - eno1
```

### SSH
**Problem:** `Permission denied (publickey).`

Change `PasswordAuthentication no` to `PasswordAuthentication yes` in `/etc/ssh/sshd_config`

```
# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
```

### Rsync
Exclude Multiple Files and Directories
* **source** : username@ip_source `ubuntu@10.10.10.12` , directory `/home/ubuntu/zurmo`
* **destination** : directory `/var/www/html`
* **exclude dir** : `app/protected/runtime/uploads/localfiles/*`

```shell
$ rsync -avP --exclude 'app/protected/runtime/uploads/localfiles/*'  ubuntu@10.10.10.12:/home/ubuntu/zurmo /var/www/html
```

### [Configure python3 as default interpretter][1]
```shell
#configure an alias
$ echo "alias python=python3" >> ~/.bash_aliases

#updated information
$ source ~/.bash_aliases

$ python --version
Python 3.7.5

```

### Configure pip3
```shell
#install latest version of PIP for Python3
$ sudo apt install python3-pip

#
$ echo "alias pip=pip3" >> ~/.bash_aliases
$ source ~/.bash_aliases

$ pip --version
pip 18.1 from /usr/lib/python3/dist-packages/pip (python 3.7)
```

### Create a Python Virtual Environment
```shell
$ sudo -H pip3 install virtualenv
# change directory to `myproject`
$ cd myproject
# create virtualenv `env`
$ virtualenv env
# activate the virtual environment
$ source env/bin/activate
```

[1]: https://garywoodfine.com/configure-python-3-4-default-ubuntu-14-04-2/ "Configure Python 3 as default on Ubuntu"
