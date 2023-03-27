---
layout : post
title : "Resizing ZFS Filesystem in LXD"
categories : [lxc]
published : true
---

 Refer to : [Resizing zfs filesystem in LXD container](https://discuss.linuxcontainers.org/t/resizing-zfs-filesystem-in-lxd-container/8465)

#### Show pool status
 ```shell
 $ sudo zpool status -v
  pool: default
 state: ONLINE
  scan: scrub repaired 0B in 0 days 00:10:50 with 0 errors on Sun Aug 14 00:34:52 2022
config:

	NAME                                          STATE     READ WRITE CKSUM
	default                                       ONLINE       0     0     0
	  /var/snap/lxd/common/lxd/disks/default.img  ONLINE       0     0     0

errors: No known data errors
 ```

#### Stop instance and `lxd` service
 ``` 
$ lxc stop instance_name 
$ sudo snap stop lxd 
 ```

#### Increase volume
```shell
$ sudo truncate -s +20G /var/snap/lxd/common/lxd/disks/default.img

$ sudo snap start lxd 
$ sudo zpool set autoexpand=on default
$ sudo zpool online -e default /var/snap/lxd/common/lxd/disks/default.img
$ sudo zpool set autoexpand=off default
 ```


