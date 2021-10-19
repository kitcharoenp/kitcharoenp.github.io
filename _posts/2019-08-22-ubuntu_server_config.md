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

**Note** the `optional: true` key declarations that *allow booting to occur without waiting for* those interfaces to activate fully.


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
      optional: true
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

### Shell Prompt
**~/.bashrc:**
find then edit 
```shell
if [ "$color_prompt" = yes ]; then
    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u\[\033[00m\]:\[\033[01;34m\]\W\[\033[00m\]\$ '
else
# disable show hostname
    PS1='${debian_chroot:+($debian_chroot)}\u:\W\$ '
fi
```
