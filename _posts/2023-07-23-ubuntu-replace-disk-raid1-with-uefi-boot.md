---
layout : post
title : "Ubuntu replace disk raid1 with uefiboot"
categories : [ubuntu, raid, efi]
published : false
---


* Server have 2 disks with raid partitions but own EFI system partition

### /dev/sda is failing and need to be replaced.
* Backup EFI partition
* Remove disk from md
* Physicaly replace disks
* clone partitions and restore efi partition from DD img

### [Copy the partition table to the new disk](https://www.thegeekdiary.com/replacing-a-failed-mirror-disk-in-a-software-raid-array-mdadm/)

Copy the partition table `/dev/sda` to the new disk `/dev/sdb` (Caution: This sfdisk command will replace the entire partition table on the target disk with that of the source disk – use an alternative command if you need to preserve other partition information):

```shell
$ sudo sfdisk -d /dev/sda | sfdisk /dev/sdb
```

* Add RAID partitions back to md
* Update grub

### Reference
* [How to properly replace disk RAID1 with uefi boot](https://superuser.com/questions/1698053/how-to-properly-replace-disk-raid1-with-uefi-boot)

* [Rebuilding Failed Redundant Boot Drives](https://knowledgebase.45drives.com/kb/rebuilding-failed-redundant-boot-drives/)

* [Exchanging hard disks in a Software-RAID](https://docs.hetzner.com/robot/dedicated-server/raid/exchanging-hard-disks-in-a-software-raid/)

* [How to Remove a Drive From a RAID Array](https://delightlylinux.wordpress.com/2020/12/22/how-to-remove-a-drive-from-a-raid-array/)
