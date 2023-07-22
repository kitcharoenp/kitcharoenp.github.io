---
layout : post
title : "Rebuilding Failed Redundant Boot Raid Drives"
categories : [raid]
published : false
---


### [Copy the partition table to the new disk](https://www.thegeekdiary.com/replacing-a-failed-mirror-disk-in-a-software-raid-array-mdadm/)

Copy the partition table `/dev/sda` to the new disk `/dev/sdb` (Caution: This sfdisk command will replace the entire partition table on the target disk with that of the source disk – use an alternative command if you need to preserve other partition information):

```shell
$ sudo sfdisk -d /dev/sda | sfdisk /dev/sdb
```


> However, when multiple arrays share the same drives [(different partitions of the same drive),](https://unix.stackexchange.com/questions/734715/multiple-mdadm-raid-rebuild-in-parallel) the rebuilds are delayed, since running in parallel here would not speed things up, but slow them down instead.

**resync=DELAYED** due to recovery `md124`
```shell
$ sudo cat /proc/mdstat
md124 : active raid1 sda6[0] sdb6[1]
      445996032 blocks super 1.2 [2/1] [_U]
      [==============>......]  recovery = 70.6% (315020864/445996032) finish=23.3min speed=93380K/sec
      bitmap: 2/4 pages [8KB], 65536KB chunk

md125 : active raid1 sda4[1] sdb4[0]
      262011904 blocks super 1.2 [2/1] [U_]
      	resync=DELAYED
      bitmap: 2/2 pages [8KB], 65536KB chunk

```


### Bootloader installation



### Reference
* [Rebuilding Failed Redundant Boot Drives](https://knowledgebase.45drives.com/kb/rebuilding-failed-redundant-boot-drives/)

* [Exchanging hard disks in a Software-RAID](https://docs.hetzner.com/robot/dedicated-server/raid/exchanging-hard-disks-in-a-software-raid/)

* [How to Remove a Drive From a RAID Array](https://delightlylinux.wordpress.com/2020/12/22/how-to-remove-a-drive-from-a-raid-array/)