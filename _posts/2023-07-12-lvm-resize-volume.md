---
layout : post
title : "Resize the Default LVM Volume on Ubuntu"
categories : [lvm, ubuntu]
published : true
---

### Verify
Run a test first to verify that it can resize properly before you actually modify the partition

```shell
$ sudo lsblk 
[sudo] password for ubuntu: 
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0 111.9M  1 loop /snap/lxd/24322
loop1                       7:1    0  49.8M  1 loop /snap/snapd/18357
...
sda                         8:0    0 931.5G  0 disk 
├─sda1                      8:1    0     1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
└─sda3                      8:3    0 928.5G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0   100G  0 lvm  /
sdb                         8:16   0 931.5G  0 disk 
├─sdb1                      8:17   0     1G  0 part 
├─sdb2                      8:18   0     2G  0 part 
└─sdb3                      8:19   0 928.5G  0 part 

$ sudo df -h | grep ubuntu
/dev/mapper/ubuntu--vg-ubuntu--lv   98G  7.0G   86G   8% /
```
Now that we know the `ubuntu--vg-ubuntu--lv` logical volume is only 98G.

### Test Resize
we can resize the `ubuntu--vg-ubuntu--lv` logical volume to the `+20%FREE` space.

```shell
$ sudo lvresize -t -v -l +20%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```

output:
```
TEST MODE: Metadata will NOT be updated and volumes will not be (de)activated.
Converted 20%FREE into at most 42417 physical extents.
Test mode: Skipping archiving of volume group.
Extending logical volume ubuntu-vg/ubuntu-lv to up to 265.69 GiB
Size of logical volume ubuntu-vg/ubuntu-lv changed from 100.00 GiB (25600 extents) to 265.69 GiB (68017 extents).
Test mode: Skipping backup of volume group.
Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

`Logical volume ubuntu-vg/ubuntu-lv successfully resized.`

### Resize the logical volume
```shell
$ sudo lvresize -v -l +20%FREE /dev/mapper/ubuntu--vg-ubuntu--lv
```

output:
```
Converted 20%FREE into at most 42417 physical extents.
Archiving volume group "ubuntu-vg" metadata (seqno 2).
Extending logical volume ubuntu-vg/ubuntu-lv to up to 265.69 GiB
Size of logical volume ubuntu-vg/ubuntu-lv changed from 100.00 GiB (25600 extents) to 265.69 GiB (68017 extents).
Loading table for ubuntu--vg-ubuntu--lv (253:0).
Suspending ubuntu--vg-ubuntu--lv (253:0) with device flush
Resuming ubuntu--vg-ubuntu--lv (253:0).
Creating volume group backup "/etc/lvm/backup/ubuntu-vg" (seqno 3).
Logical volume ubuntu-vg/ubuntu-lv successfully resized.
```

### Verify

```
$ sudo lsblk 
NAME                      MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
loop0                       7:0    0 111.9M  1 loop /snap/lxd/24322
....
sda                         8:0    0 931.5G  0 disk 
├─sda1                      8:1    0     1G  0 part /boot/efi
├─sda2                      8:2    0     2G  0 part /boot
....
└─sda3                      8:3    0 928.5G  0 part 
  └─ubuntu--vg-ubuntu--lv 253:0    0 265.7G  0 lvm  /
...
sdb                         8:16   0 931.5G  0 disk 
├─sdb1                      8:17   0     1G  0 part 
├─sdb2                      8:18   0     2G  0 part 
└─sdb3                      8:19   0 928.5G  0 part 
```
Now that the logical volume is 265.7G

### Resize the filesystem

check:
```shell
$ sudo df -h -T | grep vg
/dev/mapper/ubuntu--vg-ubuntu--lv ext4    98G  7.1G   86G   8% /
```
Filesystem Size : **98G**

resize:
```shell
$ sudo resize2fs -p /dev/mapper/ubuntu--vg-ubuntu--lv

resize2fs 1.46.5 (30-Dec-2021)
Filesystem at /dev/mapper/ubuntu--vg-ubuntu--lv is mounted on /; on-line resizing required
old_desc_blocks = 13, new_desc_blocks = 34
The filesystem on /dev/mapper/ubuntu--vg-ubuntu--lv is now 69649408 (4k) blocks long.

```

validate:
```shell
$ sudo df -h -T | grep vg
/dev/mapper/ubuntu--vg-ubuntu--lv ext4   261G  7.1G  243G   3% /
```
Filesystem Size : **261G**

### Reference
* [Resize the Default LVM Volume on Ubuntu 18.04](https://slice2.com/2020/12/05/howto-easily-resize-the-default-lvm-volume-on-ubuntu-18-04/)