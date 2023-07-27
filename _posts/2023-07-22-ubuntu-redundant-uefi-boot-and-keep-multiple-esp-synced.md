---
layout : post
title : "Redundant UEFI boot And Keep Multiple ESP Synced On Ubuntu"
categories : [ubuntu, raid, esp]
published : true
---
ESP - Efi System Partition 

> One of the traditional and old problems with UEFI booting on servers is that it had a bad story if you wanted to be able to boot off multiple disks. Each disk needed its own EFI System Partition (ESP) and you either manually kept them in synchronization (perhaps via rsync in a cron job) or put them in a Linux software RAID mirror with the RAID superblock at the end and hoped hard that nothing ever went wrong. 
[reference: Ubuntu 22.04 with multiple disks and (U)EFI booting](https://utcc.utoronto.ca/~cks/space/blog/linux/Ubuntu2204MultiDiskUEFI)


### Partitioning Scheme:

![ubuntu_storage_configuration](/assets/img/blog/ubuntu_storage_configuration.png)

see : *primary ESP, to be formatted as fat32* and *backup ESP, to be formatted as fat32*



In [the 22.04 server installer](https://knowledgebase.45drives.com/kb/kb450289-ubuntu-20-04-redundant-os-installation/), if you mark additional disks as `extra boot drives`, it will create an ESP partition on them and add them to this list of configured **ESPs**.

UEFI and RAID with GPT disks.

```shell
$ sudo parted -l

Model: ATA MB1000GDUNU (scsi)
Disk /dev/sda: 1000GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  1128MB  1127MB  fat32              boot, esp
 2      1128MB  2202MB  1074MB
 3      2202MB  6497MB  4295MB
 4      6497MB  275GB   268GB
 5      275GB   543GB   268GB
 6      543GB   1000GB  457GB


Model: ATA MB1000GDUNU (scsi)
Disk /dev/sdb: 1000GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  1128MB  1127MB  fat32              boot, esp
 2      1128MB  2202MB  1074MB
 3      2202MB  6497MB  4295MB
 4      6497MB  275GB   268GB
 5      275GB   543GB   268G
```

```
Model: Linux Software RAID Array (md)
Disk /dev/md127: 1072MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system  Name  Flags
 1      1049kB  1071MB  1070MB  ext4


Model: Linux Software RAID Array (md)
Disk /dev/md125: 268GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End    Size   File system  Name  Flags
 1      1049kB  268GB  268GB  btrfs


Model: Linux Software RAID Array (md)
Disk /dev/md123: 457GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End    Size   File system  Name  Flags
 1      1049kB  457GB  457GB  btrfs


Model: Linux Software RAID Array (md)
Disk /dev/md126: 4290MB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End     Size    File system     Name  Flags
 1      1049kB  4289MB  4288MB  linux-swap(v1)        swap


Model: Linux Software RAID Array (md)
Disk /dev/md124: 268GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags: 

Number  Start   End    Size   File system  Name  Flags
 1      1049kB  268GB  268GB  btrfs
```



```
$ sudo lsblk /dev/sda
NAME          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
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
│   └─md125p1 259:3    0 249.9G  0 part  /
├─sda5          8:5    0   250G  0 part  
│ └─md123       9:123  0 249.9G  0 raid1 
│   └─md123p1 259:2    0 249.9G  0 part  /poo1
└─sda6          8:6    0 425.5G  0 part  
  └─md124       9:124  0 425.3G  0 raid1 
    └─md124p1 259:4    0 425.3G  0 part  /pool2
$ sudo lsblk /dev/sdb
NAME          MAJ:MIN RM   SIZE RO TYPE  MOUNTPOINTS
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
│   └─md125p1 259:3    0 249.9G  0 part  /
├─sdb5          8:21   0   250G  0 part  
│ └─md123       9:123  0 249.9G  0 raid1 
│   └─md123p1 259:2    0 249.9G  0 part  /poo1
└─sdb6          8:22   0 425.5G  0 part  
  └─md124       9:124  0 425.3G  0 raid1 
    └─md124p1 259:4    0 425.3G  0 part  /pool2
```


`/boot/efi` is mounted because of this entry in` /etc/fstab` added by Ubuntu installation

```shell
$ sudo grep efi /etc/fstab
# /boot/efi was on /dev/sdb1 during curtin installation
/dev/disk/by-uuid/1F01-E5A8 /boot/efi vfat defaults 0 1
```

**Does Ubuntu takes care of ESP sync? Given the case that I lost currently mounted /boot/efi, are backup ESP up to date? Otherwise, do I have to manually mount and sync them all?**

> Yes, if `grub-efi-amd64` is configured to use those as the ESP:s then Ubuntu will automatically sync them all. And they don't need to be mounted to be synced, myself I don't have `/boot/efi` mounted in `fstab` at all. [Reference : Ubuntu installation keep multiple ESP synced](https://unix.stackexchange.com/questions/719194/does-ubuntu-installation-keep-multiple-esp-synced-how-to-setup-etc-fstab-to-fa)



### [Installing and configuring the GRUB boot loader to a software-based RAID1 array](https://help.ggcircuit.com/knowledge/appendix-iii-install-the-grub-boot-loader)
> The Debian OS installer by default only installs the GRUB boot loader to the main hard disk in the software-based RAID array. It is best to install the boot loader to all disks in the RAID1 array in the event that (1) a drive failure occurs and you need to boot from the secondary drive or (2) due to swapping hard drive connections physically in the chassis, the other drive becomes the primary drive.



### [Reconfiguring grub](https://unix.stackexchange.com/questions/621942/mirroring-efi-system-partition-esp-on-ubuntu)
```shell
$ sudo dpkg-reconfigure grub-efi-amd64
```
> The grub reconfigure script then checks for all partitions with the ESP GPT type and allows the user to select both. **After that change future package updates/re-installs will update both ESPs.**


![grub-efi-amd64-1](/assets/img/blog/grub-efi-amd64_1.png)

![grub-efi-amd64-2](/assets/img/blog/grub-efi-amd64_2.png)


output:
```
Installing grub to /boot/efi.
Installing for x86_64-efi platform.
Installation finished. No error reported.
Installing grub to /var/lib/grub/esp.
Installing for x86_64-efi platform.
Installation finished. No error reported.
Sourcing file `/etc/default/grub'
Sourcing file `/etc/default/grub.d/init-select.cfg'
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-5.15.0-76-generic
Found initrd image: /boot/initrd.img-5.15.0-76-generic
Warning: os-prober will not be executed to detect other bootable partitions.
Systems on them will not be added to the GRUB boot configuration.
Check GRUB_DISABLE_OS_PROBER documentation entry.
Adding boot menu entry for UEFI Firmware Settings ...
done
Processing triggers for shim-signed (1.51.3+15.7-0ubuntu1) ...
```

### Show which devices grub is configured to use 

```shell
$ sudo debconf-show grub-efi-amd64
```
```
  grub2/no_efi_extra_removable: false
  grub2/kfreebsd_cmdline:
* grub2/linux_cmdline:
  grub2/kfreebsd_cmdline_default: quiet splash
* grub2/linux_cmdline_default:
  grub2/unsigned_kernels_title:
  grub-efi/install_devices_disks_changed:
* grub-efi/install_devices: /dev/disk/by-id/ata-MB1000GDUNU_17LDK2VMF1EA-part1, /dev/disk/by-id/ata-MB1000GDUNU_17LQK041F1EA-part1
  grub-efi/install_devices_empty: false
  grub2/unsigned_kernels:
  grub-efi/install_devices_failed: false
  grub-efi/partition_description:
* grub2/update_nvram: true
```

```
* grub-efi/install_devices: /dev/disk/by-id/ata-MB1000GDUNU_17LDK2VMF1EA-part1, /dev/disk/by-id/ata-MB1000GDUNU_17LQK041F1EA-part1
```

### Reference

* [Ubuntu installation keep multiple ESP synced](https://unix.stackexchange.com/questions/719194/does-ubuntu-installation-keep-multiple-esp-synced-how-to-setup-etc-fstab-to-fa)

* [GRUB boot loader to a software-based RAID1 array](https://help.ggcircuit.com/knowledge/appendix-iii-install-the-grub-boot-loader)

* [Mirroring EFI System Partition (ESP) on Ubuntu](https://unix.stackexchange.com/questions/621942/mirroring-efi-system-partition-esp-on-ubuntu)

* [Ubuntu 22.04 with multiple disks and (U)EFI booting](https://utcc.utoronto.ca/~cks/space/blog/linux/Ubuntu2204MultiDiskUEFI?showcomments)

* [Ubuntu 20.04 Redundant OS Installation](https://knowledgebase.45drives.com/kb/kb450289-ubuntu-20-04-redundant-os-installation/)












