---
layout : post
title : "Exchanging hard disks in a Software-RAID"
categories : [raid]
published : false
---

### List information about block devices

```shell
$ sudo lsblk
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
    └─md124p1 259:4    0 425.3G  0 part  /pool
```
Reference to [Ubuntu 20.04 Redundant OS Installation](https://knowledgebase.45drives.com/kb/kb450289-ubuntu-20-04-redundant-os-installation/)


There are five partitions in total:

* `/dev/md123` as `/poo1` 
* `/dev/md124` as `/pool2` 
* `/dev/md125` as `/` 
* `/dev/md126` as `swap` 
* `/dev/md127` as `/boot`


### Write all disk caches to the disk

Before removing raid disks, please make sure you run the following command to write all disk caches to the disk:

```shell
$ sudo sync
```


### Result after removing disk `sda` and reinstall

`/dev/sda` is the defective drive in this case. A missing or **defective** drive is shown by **[U_]** and/or **[_U]**. If the RAID array is intact, it shows [UU].


> We [can’t remove a disk directly from the array](https://www.24x7serversupport.com/blog/how-to-create-remove-the-disk-from-the-array/), unless it is failed, so we first have to fail it (if the drive it is failed this is normally already in failed state and this step is not needed):


```shell
$ sudo cat /proc/mdstat 
Personalities : [raid1] [linear] [multipath] [raid0] [raid6] [raid5] [raid4] [raid10] 
md123 : active raid1 sdb5[1]
      262011904 blocks super 1.2 [2/1] [_U]
      bitmap: 2/2 pages [8KB], 65536KB chunk

md124 : active raid1 sda6[0] sdb6[1]
      445996032 blocks super 1.2 [2/1] [_U]
      bitmap: 4/4 pages [16KB], 65536KB chunk

md125 : active raid1 sdb4[0]
      262011904 blocks super 1.2 [2/1] [U_]
      bitmap: 2/2 pages [8KB], 65536KB chunk

md126 : active raid1 sda3[1] sdb3[0]
      4189184 blocks super 1.2 [2/2] [UU]
      
md127 : active raid1 sdb2[0]
      1046528 blocks super 1.2 [2/1] [U_]
      
unused devices: <none>
```

### Verify

Verify a missing or **defective** drive of `/dev/md125`

```shell
$ sudo mdadm --detail /dev/md125
```

```
/dev/md125:
           Version : 1.2
     Creation Time : Fri Jul 21 05:06:08 2023
        Raid Level : raid1
        Array Size : 262011904 (249.87 GiB 268.30 GB)
     Used Dev Size : 262011904 (249.87 GiB 268.30 GB)
      Raid Devices : 2
     Total Devices : 1
       Persistence : Superblock is persistent

     Intent Bitmap : Internal

       Update Time : Fri Jul 21 08:44:01 2023
             State : active, degraded 
    Active Devices : 1
   Working Devices : 1
    Failed Devices : 0
     Spare Devices : 0

Consistency Policy : bitmap

              Name : ubuntu-server:root
              UUID : 6212e4a8:55b1f7a8:e07de780:edd75613
            Events : 1251

    Number   Major   Minor   RaidDevice State
       0       8       20        0      active sync   /dev/sdb4
       -       0        0        1      removed

```

```
             State : active, degraded 
...             
    Number   Major   Minor   RaidDevice State
       0       8       20        0      active sync   /dev/sdb4
       -       0        0        1      removed

```


### Rebuild
Create the mirror of the disk:

```shell
# integrate into the RAID array
$ sudo mdadm --manage /dev/md125 --add /dev/sda4

# show detail
$ sudo mdadm --detail /dev/md125
```


```shell
/dev/md125:
           Version : 1.2
     Creation Time : Fri Jul 21 05:06:08 2023
        Raid Level : raid1
        Array Size : 262011904 (249.87 GiB 268.30 GB)
     Used Dev Size : 262011904 (249.87 GiB 268.30 GB)
      Raid Devices : 2
     Total Devices : 2
       Persistence : Superblock is persistent

     Intent Bitmap : Internal

       Update Time : Fri Jul 21 08:47:46 2023
             State : clean, degraded, resyncing (DELAYED) 
    Active Devices : 1
   Working Devices : 2
    Failed Devices : 0
     Spare Devices : 1

Consistency Policy : bitmap

              Name : ubuntu-server:root
              UUID : 6212e4a8:55b1f7a8:e07de780:edd75613
            Events : 1261

    Number   Major   Minor   RaidDevice State
       0       8       20        0      active sync   /dev/sdb4
       1       8        4        1      spare rebuilding   /dev/sda4

```

```
             State : clean, degraded, resyncing (DELAYED)
...
    Number   Major   Minor   RaidDevice State
       0       8       20        0      active sync   /dev/sdb4
       1       8        4        1      spare rebuilding   /dev/sda4
```

Rebuilding `md124`  then `md125` stats as `clean, degraded, resyncing (DELAYED)`

> However, when multiple arrays share the same drives [(different partitions of the same drive),](https://unix.stackexchange.com/questions/734715/multiple-mdadm-raid-rebuild-in-parallel) the rebuilds are delayed, since running in parallel here would not speed things up, but slow them down instead.


###  Check the status of the synchronization
```shell
$ sudo cat /proc/mdstat
```

```
md124 : active raid1 sda6[0] sdb6[1]
      445996032 blocks super 1.2 [2/1] [_U]
      [==============>......]  recovery = 70.6% (315020864/445996032) finish=23.3min speed=93380K/sec
      bitmap: 2/4 pages [8KB], 65536KB chunk

md125 : active raid1 sda4[1] sdb4[0]
      262011904 blocks super 1.2 [2/1] [U_]
      	resync=DELAYED
      bitmap: 2/2 pages [8KB], 65536KB chunk
```


### Reference
* [Exchanging hard disks in a Software-RAID](https://docs.hetzner.com/robot/dedicated-server/raid/exchanging-hard-disks-in-a-software-raid/)

* [How to Remove a Drive From a RAID Array](https://delightlylinux.wordpress.com/2020/12/22/how-to-remove-a-drive-from-a-raid-array/)