---
layout : post
title : "ZFS : Tunning and  Optimisation for MySQL / MariaDB"
categories : [mysql, zfs, mariadb]
published : true
---

### MySQL on ZFS - Performance

[8]: https://www.usenix.org/system/files/login/articles/login_winter16_09_jude.pdf "Tuning OpenZFS"

> The `recordsize` property gives the maximum size of a logical block in a ZFS dataset. The
default recordsize is 128 KB. Many database engines prefer
smaller blocks, such as 4 KB or 8 KB. It makes sense to change the `recordsize` on datasets
dedicated to such files. \[[8]\]

> The most important tuning you can perform for an application is the dataset block size. If an
application consistently writes blocks of a certain size, `recordsize` should match the block
size used by the application. This becomes really important with databases \[[8]\]

> Write `amplification` happens when changing a small amount of data requires writing a large amount of data. Suppose you must change
8 KB in the middle of a 128 KB block. ZFS must read the 128 KB, modify 8 KB somewhere
in it, calculate a new checksum, and write the new 128 KB block. \[[8]\]


> Newer versions of OpenZFS also contain a `redundant_meta-data` property, which defaults to `all`. This is the original behavior from previous versions of ZFS. However, this property can also
be set to `most`, which causes ZFS to reduce the number of copies
of some types of metadata that it keeps. \[[8]\]


> Depending on your needs and workload, allowing the database
engine to manage caching might be better. ZFS defaults to cach-
ing much or all of the data from your database in the ARC, while
the database engine keeps its own cache, resulting in wasteful
double caching. Setting the `primarycache` property to `metadata`
rather than the default `all` tells ZFS to avoid caching actual data
in the ARC. The secondarycache property similarly controls the
L2ARC. \[[8]\]



[6]: https://shatteredsilicon.net/blog/2020/06/05/mysql-mariadb-innodb-on-zfs/ "MYSQL ON ZFS"

[7]: https://github.com/letsencrypt/openzfs-nvme-databases "ZFS datastore for MariaDB"

### [ZFS TUNING AND OPTIMISATION][6]

```
sync=disabled
atime=off
compression=lz4
logbias=throughput
primarycache=metadata
recordsize=16k
xattr=sa
redundant_metadata=most
```

* `sync=disabled` : disable all explicit disk flushing to any one file system. The background commit thread will still flush the written data to disk every 5 seconds, but flushing the data once per 5 seconds instead of for each transaction

### zfs set sync=disabled 
In technical terms, `sync=disabled` tells ZFS “when an application requests that you sync() before returning, lie to it.” If you don’t have applications explicitly calling sync(), this doesn’t result in any difference at all. If you do, it tremendously increases write performance… but, remember, it does so by lying to applications that specifically request that a set of data be safely committed to disk before they do anything else. TL;DR: don’t do this unless you’re absolutely sure you don’t give a crap about your applications’ data consistency safeguards! \[[4]\]

[4]: https://jrs-s.net/2019/07/20/zfs-set-syncdisabled/ "zfs set sync=disabled"

* `compression=lz4` : LZ4 compression is very fast. So fast, in fact, that if you have a modern processor, you are likely to be able to decompress the data much faster than your SSD can read the compressed data. 

* `logbias=throughput` : “If logbias is set to throughput, ZFS does not use the pool’s separate log devices. Instead, ZFS optimizes synchronous operations for global pool throughput and efficient use of resources. The default value is latency. For most configurations, the default value is recommended. Using the logbias=throughput value might **improve performance for writing database** files.”


* `primarycache=metadata` Since InnoDB already does it’s own caching via the Buffer Pool, there is no benefit to also caching the data in the page cache, or in the case of ZFS, ARC. Since `O_DIRECT` doesn’t disable caching on ZFS, we can disable caching of data in the ARC by setting `primarycache` to only cache metadata (default is “all”).

* `recordsize=16k` :  InnoDB uses 16KB pages. Using this parameter, we tell ZFS to limit the block size to 16KB. This prevents multiple pages from being written in one larger block, as that would necessitate reading the entire larger block for future updates to any one page in that block. 

* `xattr=sa`: This instructs ZFS to store extended file attributes in the file’s inode instead of a hidden directory, since storing it in the inode is more efficient. This is only really relevant on systems that use SELinux, since this uses extended attributes for storing security contexts of files.

* `autoreplace=on` : automatically activate hot spare drives if another drive fails  \[[7]\]

* `redundant_metadata=most` : ZFS stores an extra copy of all metadata by default, beyond the redundancy provided by mirroring. Because we're prioritizing performance for a write-intensive workload, we lower this level of redundancy


**command:**
```
sudo zfs set  sync=disabled \
atime=off \
compression=lz4 \
logbias=throughput \
primarycache=metadata \
recordsize=16k \
xattr=sa \
redundant_metadata=most  default/containers/NAME
```

* **default** : zfs pool name
* **NAME** : lxc container name

### [Querying ZFS Properties](https://docs.oracle.com/cd/E19253-01/819-5461/gazuk/index.html)

```
sudo zfs get all default/containers/NAME
``` 

### [MYSQL TUNING AND OPTIMIZATION FOR ZFS][6]
```
# tunning for zfs 
innodb_log_write_ahead_size = 16384
innodb_doublewrite = 0
innodb_use_native_aio = 0
innodb_use_atomic_writes = 0
innodb_compression_default = OFF

innodb_flush_log_at_trx_commit=0 # disable disk flushing
sync_binlog = 0 # disable disk flushing

innodb_file_per_table = ON
```

* **[innodb_log_write_ahead_size=16384](https://mariadb.com/kb/en/innodb-system-variables/#innodb_log_write_ahead_size)**  Optimise the interaction between the storage subsystem and MySQL / MariaDB. InnoDB defaults to 16KB page size for tablespaces, using the `innodb_log_write_ahead_size` option. That reduces the requirement to 16KB blocks for the InnoDB tablespaces and logs, and no fixed block size for the replication logs. If we are keeping the InnoDB logs in the data directory (as is the default), we should set this to what we set the ZFS `recordsize=16K` to.


* **[innodb_doublewrite = 0](https://mariadb.com/kb/en/innodb-system-variables/#innodb_doublewrite):** InnoDB double-write has one purpose – to prevent torn pages from corrupting the data in case of application crash. Because commits on ZFS are atomic, and we have aligned the InnoDB page size and innodb_log_write_ahead_size with ZFS recordsize, a torn page cannot occur – either the entire block is written, or the entire block is lost.


* **[innodb_flush_neighbors=0](https://mariadb.com/kb/en/innodb-system-variables/#innodb_flush_neighbors):** When it is time to flush out a page to disk, InnoDB also flushes all of the nearby pages as well. This is beneficial on spinning rust and with traditional file systems,  this typically just wastes disk I/O. It is better to disable it on ZFS, even when using mechanical disks.

* **[innodb_use_native_aio=0](https://mariadb.com/kb/en/innodb-system-variables/#innodb_use_native_aio):** On Linux, the ZFS driver’s AIO implementation is a compatibility shim.

* **[innodb_use_atomic_writes=0](https://mariadb.com/kb/en/innodb-system-variables/#innodb_use_atomic_writes):** fully use of ZFS native AIO

* **[innodb_compression_default=OFF][a]:** ZFS LZ4 compression is incredibly fast, so we should leave compression to ZFS and not use InnoDB’s built in page compression.  Leaving compression to ZFS doesn’t disrupt the block alignment.

* **[innodb_flush_log_at_trx_commit=0](https://mariadb.com/kb/en/innodb-system-variables/#innodb_flush_log_at_trx_commit):** disable disk flushing to temporarily boost write performance without restarting mysqld 

* **sync_binlog=0:** disable disk flushing


* **[innodb_file_per_table=ON](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table):** store tables in individual files, for much easier backup, recovery, or relocation:  \[[7]\]

* **[innodb_io_capacity=1000](https://mariadb.com/kb/en/innodb-system-variables/#innodb_io_capacity):** increase target IOPS above the defaults. \[[7]\]

* **[innodb_io_capacity_max=2500](https://mariadb.com/kb/en/innodb-system-variables/#innodb_io_capacity_max):** increase max IOPS above the defaults. \[[7]\]
 

[a]: https://mariadb.com/kb/en/innodb-system-variables/#innodb_compression_default "innodb_compression_default"


### SECTOR SIZE
Most modern disks have 4KB hardware sectors, even of they emulate 512 byte sectors.
**4KB** writes is the most commonly used block size on most file systems. it is usually a good idea to explicitly instruct it to create a pool with 4KB sector size using the ashift parameter:

```
zpool create -o ashift=12 tank
```


### [Tuning ZFS for Database Products](https://docs.oracle.com/cd/E26502_01/html/E29022/chapterzfs-db1.html)



### Benchmark

* [MySQL/ZFS Performance](https://www.percona.com/blog/mysql-zfs-performance-update/)

* [MySQL/ZFS in the Cloud](https://www.percona.com/blog/mysql-zfs-in-the-cloud-leveraging-ephemeral-storage/)






