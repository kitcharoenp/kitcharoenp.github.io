---
layout : post
title : "RAID 1 of /boot/efi partition on Debian"
categories : [ubuntu, raid, efi]
published : false
---

https://discourse.maas.io/t/support-root-on-software-raid-in-uefi-mode/1079

* I've installed Debian without setting up the RAID for the ESP partition. During the partitioning, I've already created two identical partitions marked as ESP partitions. They were on /dev/sda1 and /dev/sdb1
I've copied the contents of /boot/efi somewhere else (/boot/eficopy).
* umount /boot/efi
* mdadm --create --verbose /dev/md3 --level=1 --raid-devices=2 --metadata=1.0 /dev/sda1 /dev/sdb1. Of course change /dev/md3 to something else if /dev/md3 is already an active MD device
* mkfs.vfat /dev/md3
* found the UUID of the partition in /dev/disk/by-uuid
* changed the /boot/efi entry in /etc/fstab with the new UUID
* mount /boot/efi
* copied the data from the backup into /boot/efi again


### Reference
* [RAID 1 of /boot/efi partition on Debian](https://unix.stackexchange.com/questions/644108/raid-1-of-boot-efi-partition-on-debian)


### show which devices grub is configured to use.

### efibootmgr -v

### 1 Put the ESP in a v1.0 mdraid level 1

### 2. 
* Manually sync the ESP to another partition which can be used if
the first device dies.

* copy the first /efi partition to the second /efi partition every time the content of the first is changed. 

- set the boot flag on the array, not on the physical partitions
- if the efi files are copied according to boot flag, they will be copied to the raid1 array and be raided, so if the primary disk fails in theory it should keep booting from the secondary

using dd because it was dead simple
