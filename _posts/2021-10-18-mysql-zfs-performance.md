---
layout : post
title : "MySQL : MySQL/ZFS Performance"
categories : [mysql]
published : false
---

### MySQL on ZFS - Performance

One thing I’d strongly recommend is that you provide a full disk or partition to back your zpool and not use a loop drive in production as that’s considerably slower. \[[1]\]

The few tweaks I’ve seen related to this are around maybe **disabling atime on the dataset or tweaking the recordsize.** Those are things you can do live so it should be easy to see if they make any difference. \[[1]\]

```shell
zfs set compress=no lxd/containers/NAME
zfs set atime=off lxd/containers/NAME
zfs set recordsize=128k lxd/containers/NAME
```

[1]: https://discuss.linuxcontainers.org/t/mysql-on-zfs-performance/9308/2 "MySQL on ZFS - Performance"

Testing atime=off/on shows nearly no difference, but theoretically atime=off should be better for several containers each running their own MySQL server. \[[2]\]


[2]: https://discuss.linuxcontainers.org/t/mysql-on-zfs-performance/9308/8 "MySQL on ZFS - Performance"


ARC is global, shared by all ZFS pools on your system. You’ll often want to set a maximum size just to avoid freaking out monitoring systems.

ZFS ARC doesn’t show as cached memory but instead as used, even though, just like cache, it will get freeed as memory pressure increases on the system. The result is that monitoring systems will think you’re running out of memory when it’s in fact just ZFS doing a bunch of caching and applications still very much being able to allocate memory if they need it.

Can the performance benefit from setting a larger value for ZFS ARC?
How much of a servers memory should be set?

What would be the problem/penalty with recordsize being other than 128?

Greater ARC will usually come with improved performance, though note that this is read caching, so you only ever need it to be as large as your “hot set”. On file servers serving TBs of data from slow drives without SSD caching, you may want tens of GBs of ARC.

But for most other cases where I/O to the underlying disk aren’t bad to start with and you don’t have GB/s of writes, having an ARC of just a few GBs is usually fine.

My understanding of recordsize is that it works in much the same way as a block size, so if you set it to a very high value, small writes will use at least one record and so potentially waste space. Using too small a value leads to needless work and fragmentation. The ideal value for something like a database would be to line it up with the size of writes performed by the database, so that when it writes something to disk, it fills exactly one record or more record.


I read regarding Mysql on ZFS that it is good to set the recordsize to 16 like the page size in InnoDB.

sync=disabled gave me the larges performance gain yet, from 1000 IOps (reads/s) to nearly 5000. And according to some articles I read I can only loose 5 seconds which is worth the risk for that huge speed gain.

https://jrs-s.net/2019/07/20/zfs-set-syncdisabled/


Yeah or you could even go one step further and keep all your containers on the default settings but then do:

lxc storage volume create default mysql
lxc config device add CONTAINER mysql disk pool=default source=mysql path=/var/lib/mysql
That will get you a separate dataset just for mysql which you can then tweak as you wish in ZFS.


put a different filesystem (“folder”) into the mysql path of a container \[[3]\]
```
lxc storage create container-pool zfs source=zfs/container-pool
zfs set quota=336g zfs/container-pool  ### absolute total for the whole pool
lxc storage volume create container-pool mysql-volume
zfs set recordsize=16k zfs/container-pool/custom/zfs_mysql-volume
lxc init ubuntu:20.04 container-name -s container-pool
lxc config device add container-name mysql-disk disk pool=container-pool source=mysql-volume path=/var/lib/mysql
lxc start container-name
``` 


[3]: https://discuss.linuxcontainers.org/t/mysql-on-zfs-performance/9308/31 "put a different filesystem (“folder”) into the mysql path of a container."


You can use the special keyword all to retrieve all dataset property values.
```
sudo zfs get all default
```

### zfs set sync=disabled 
In technical terms, sync=disabled tells ZFS “when an application requests that you sync() before returning, lie to it.” If you don’t have applications explicitly calling sync(), this doesn’t result in any difference at all. If you do, it tremendously increases write performance… but, remember, it does so by lying to applications that specifically request that a set of data be safely committed to disk before they do anything else. TL;DR: don’t do this unless you’re absolutely sure you don’t give a crap about your applications’ data consistency safeguards! \[[4]\]

[4]: https://jrs-s.net/2019/07/20/zfs-set-syncdisabled/ "zfs set sync=disabled"


[Adding a RAMDISK as SLOG ZIL to ZFS](https://blog.carlesmateo.com/2020/08/23/adding-a-ramdisk-as-slog-zil-to-zfs/)


I set ZFS sync=disabled on mysql datasets to increase speed
I wonder
Is there any cons/pros to set sync=disabled on all LXD containers/volumes ZFS datasets?

Increased risk of data loss and corruption in case of full system crash or power loss
Less consistent performance profile. If your system is very I/O intensive, you will be piling up those transactions in memory until you reach such memory pressure that ZFS will have no choice but block all new I/O request while it pushes what it has in memory onto disk. If your I/O profile is burty, that’s fine, you’ll effectively buffer in memory and push that to disk at a less busy time, but if busy all the time, you’ll have a problem.

[5]: https://discuss.linuxcontainers.org/t/zfs-sync-disabled-on-container/10076 "zfs set sync=disabled"





https://www.percona.com/blog/mysql-zfs-performance-update/

https://www.percona.com/blog/mysql-zfs-in-the-cloud-leveraging-ephemeral-storage/

https://docs.oracle.com/cd/E26502_01/html/E29022/chapterzfs-db1.html

http://download.nust.na/pub6/mysql/tech-resources/articles/mysql-zfs.html

https://github.com/letsencrypt/openzfs-nvme-databases


### [MYSQL TUNING AND OPTIMIZATION][6]
```
innodb_log_write_ahead_size = 16384

innodb_doublewrite = 0
innodb_checksum_algorithm = none
innodb_use_native_aio = 0
innodb_use_atomic_writes = 0
```


[6]: https://shatteredsilicon.net/blog/2020/06/05/mysql-mariadb-innodb-on-zfs/ "MYSQL ON ZFS"
