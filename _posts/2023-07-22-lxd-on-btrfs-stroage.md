---
layout : post
title : "Create LXD clustering on btrfs"
categories : [lxd]
published : true
---

### List information about block devices.

```shell
$ sudo lsblk
[sudo] password for ubuntu: 
NAME          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
loop0           7:0    0  63.3M  1 loop  /snap/core20/1822
loop1           7:1    0 111.9M  1 loop  /snap/lxd/24322
loop2           7:2    0  49.8M  1 loop  /snap/snapd/18357
loop3           7:3    0  53.3M  1 loop  /snap/snapd/19457
loop4           7:4    0  63.4M  1 loop  /snap/core20/1974
sda             8:0    0 931.5G  0 disk  
├─sda1          8:1    0     1G  0 part  
├─sda2          8:2    0     1G  0 part  
│ └─md127       9:127  0  1022M  0 raid1 
│   └─md127p1 259:0    0  1020M  0 part  /boot
├─sda3          8:3    0     4G  0 part  
│ └─md126       9:126  0     4G  0 raid1 
│   └─md126p1 259:1    0     4G  0 part  [SWAP]
├─sda4          8:4    0   250G  0 part  
│ └─md125       9:125  0 249.9G  0 raid1 
│   └─md125p1 259:2    0 249.9G  0 part  /
├─sda5          8:5    0   250G  0 part  
│ └─md123       9:123  0 249.9G  0 raid1 
│   └─md123p1 259:3    0 249.9G  0 part  /poo1
└─sda6          8:6    0 425.5G  0 part  
  └─md124       9:124  0 425.3G  0 raid1 
    └─md124p1 259:4    0 425.3G  0 part  /pool2
sdb             8:16   0 931.5G  0 disk  
├─sdb1          8:17   0     1G  0 part  /boot/efi
├─sdb2          8:18   0     1G  0 part  
│ └─md127       9:127  0  1022M  0 raid1 
│   └─md127p1 259:0    0  1020M  0 part  /boot
├─sdb3          8:19   0     4G  0 part  
│ └─md126       9:126  0     4G  0 raid1 
│   └─md126p1 259:1    0     4G  0 part  [SWAP]
├─sdb4          8:20   0   250G  0 part  
│ └─md125       9:125  0 249.9G  0 raid1 
│   └─md125p1 259:2    0 249.9G  0 part  /
├─sdb5          8:21   0   250G  0 part  
│ └─md123       9:123  0 249.9G  0 raid1 
│   └─md123p1 259:3    0 249.9G  0 part  /poo1
└─sdb6          8:22   0 425.5G  0 part  
  └─md124       9:124  0 425.3G  0 raid1 
    └─md124p1 259:4    0 425.3G  0 part  /pool2
```

```
├─sda5          8:5    0   250G  0 part  
│ └─md123       9:123  0 249.9G  0 raid1 
│   └─md123p1 259:3    0 249.9G  0 part  /poo1
```


btrfs show **/poo1:**
```
$ sudo btrfs filesystem show /poo1
Label: none  uuid: a49426a2-91f3-46ca-865a-e479260445cb
	Total devices 1 FS bytes used 144.00KiB
	devid    1 size 249.87GiB used 2.02GiB path /dev/md123p1
```

btrfs show **/dev/md123p1:**
```
ubuntu@server03:~$ sudo btrfs filesystem show /dev/md123p1
Label: none  uuid: a49426a2-91f3-46ca-865a-e479260445cb
	Total devices 1 FS bytes used 144.00KiB
	devid    1 size 249.87GiB used 2.02GiB path /dev/md123p1
```


### Prepare lxd
Set up the cluster master use the existing **Btrfs** file system at `/poo1` for **local**:

```
$ sudo lxd init
Would you like to use LXD clustering? (yes/no) [default=no]: yes
What IP address or DNS name should be used to reach this server? [default=10.10.10.13]: 
Are you joining an existing cluster? (yes/no) [default=no]: 
What member name should be used to identify this server in the cluster? [default=server03]: 
Setup password authentication on the cluster? (yes/no) [default=no]: 
Do you want to configure a new local storage pool? (yes/no) [default=yes]: 
Name of the storage backend to use (zfs, btrfs, dir, lvm) [default=btrfs]: 
Would you like to create a new btrfs subvolume under /var/snap/lxd/common/lxd? (yes/no) [default=yes]: no
Create a new BTRFS pool? (yes/no) [default=yes]: no
Name of the existing BTRFS pool or dataset: /poo1
Do you want to configure a new remote storage pool? (yes/no) [default=no]: 
Would you like to connect to a MAAS server? (yes/no) [default=no]: 
Would you like to configure LXD to use an existing bridge or host interface? (yes/no) [default=no]: 
Would you like to create a new Fan overlay network? (yes/no) [default=yes]: 
What subnet should be used as the Fan underlay? [default=auto]: 10.10.10.0/24
Would you like stale cached images to be updated automatically? (yes/no) [default=yes]: 
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: yes
```

Use **Btrfs** file system at `/poo1` for **local**
* Name of the storage backend to use (zfs, btrfs, dir, lvm) **[default=btrfs]:**
* Would you like to create a new btrfs subvolume under /var/snap/lxd/common/lxd? (yes/no)**[default=yes]: no**
* Create a new BTRFS pool? (yes/no) **[default=yes]: no**
* Name of the existing BTRFS pool or dataset: **/poo1**


```
config:
  core.https_address: 10.10.10.13:8443
networks:
- config:
    bridge.mode: fan
    fan.underlay_subnet: 10.10.10.0/24
  description: ""
  name: lxdfan0
  type: ""
  project: default
storage_pools:
- config:
    source: /poo1
  description: ""
  name: local
  driver: btrfs
profiles:
- config: {}
  description: ""
  devices:
    eth0:
      name: eth0
      network: lxdfan0
      type: nic
    root:
      path: /
      pool: local
      type: disk
  name: default
projects: []
cluster:
  server_name: server03
  enabled: true
  member_config: []
  cluster_address: ""
  cluster_certificate: ""
  server_address: ""
  cluster_password: ""
  cluster_certificate_path: ""
  cluster_token: ""
```

### Btrfs storage pools
```
storage_pools:
- config:
    source: /poo1
  description: ""
  name: local
  driver: btrfs
```

### Storage list
```
$ lxc storage list
+-------+--------+-------------+---------+---------+
| NAME  | DRIVER | DESCRIPTION | USED BY |  STATE  |
+-------+--------+-------------+---------+---------+
| local | btrfs  |             | 1       | CREATED |
+-------+--------+-------------+---------+---------+
```

### Storage show `local`
```
$ lxc storage show local
config: {}
description: ""
name: local
driver: btrfs
used_by:
- /1.0/profiles/default
status: Created
locations:
- server03

```

### Reference
* [Create a storage pool](https://documentation.ubuntu.com/lxd/en/latest/howto/storage_pools/#create-a-storage-pool)