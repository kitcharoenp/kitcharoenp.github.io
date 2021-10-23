---
layout : post
title : "LXC : Put a different filesystem (“folder”) into the mysql path"
categories : [mysql, zfs, mariadb, lxc]
published : true
---


# Put a different filesystem (“folder”) into the mysql path of a container \[[3]\]


### [Create ZFS storage pools](https://linuxcontainers.org/lxd/docs/master/storage)

Create a new zfs pool called "`container-pool`" on /dev/sdX. The ZFS Zpool will also be called "container-pool".
  
```shell
$ lxc storage create container-pool zfs source=zfs/container-pool
$ zfs set quota=336g zfs/container-pool  ### absolute total for the whole pool
```

### create storage volume to pool
```
$ lxc storage volume create container-pool mysql-volume
$ zfs set recordsize=16k zfs/container-pool/custom/zfs_mysql-volume
```


### init container to pool
```

$ lxc init ubuntu:20.04 container-name -s container-pool
```
### config device to container

```
$ lxc config device add container-name mysql-disk disk pool=container-pool source=mysql-volume path=/var/lib/mysql
```

### start container

```
$ lxc start container-name
``` 


[3]: https://discuss.linuxcontainers.org/t/mysql-on-zfs-performance/9308/31 "put a different filesystem (“folder”) into the mysql path of a container."


You can use the special keyword all to retrieve all dataset property values.
```
sudo zfs get all default