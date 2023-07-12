---
layout : post
title : "Resize the Default LVM Volume on Ubuntu"
categories : [lvm, ubuntu]
published : true
---

### Check for free space
run the command `vgdisplay` and check for free space

```shell
$ sudo vgdisplay 
```

output:
```
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID             
  Format                lvm2
...
  VG Size               <928.46 GiB
  PE Size               4.00 MiB
  Total PE              237685
  Alloc PE / Size       68017 / 265.69 GiB
  Free  PE / Size       169668 / <662.77 GiB
  VG UUID               QlCEZf-4llb-9ntT-2xhQ-7EKA-NX53-efynCX
```

### Check the Logical Volume size

```shell
$ sudo lvdisplay
```

output:
```
  --- Logical volume ---
  LV Path                /dev/ubuntu-vg/ubuntu-lv
  LV Name                ubuntu-lv
  VG Name                ubuntu-vg
  LV UUID                xP6AsV-2A5E-npbO-LV5v-1DoJ-mZZ7-gIgYCO
  LV Write Access        read/write
  LV Creation host, time ubuntu-server, 2023-07-05 07:44:40 +0000
  LV Status              available
  # open                 1
  LV Size                265.69 GiB
  Current LE             68017
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0

```

### [Creating a Logical Volume From All Remaining Free Space](https://www.digitalocean.com/community/tutorials/how-to-use-lvm-to-manage-storage-devices-on-ubuntu-18-04#creating-a-logical-volume-from-all-remaining-free-space)


create the `lxd-lv` logical volume on the `ubuntu-vg` volume group
```shell
$ sudo lvcreate -l 100%FREE -n lxd-lv ubuntu-vg
  Logical volume "lxd-lv" created.
```

**display volume group:**
```shell
$ sudo vgdisplay 
  --- Volume group ---
  VG Name               ubuntu-vg
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               1
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <928.46 GiB
  PE Size               4.00 MiB
  Total PE              237685
  Alloc PE / Size       237685 / <928.46 GiB
  Free  PE / Size       0 / 0   
  VG UUID               QlCEZf-4llb-9ntT-2xhQ-7EKA-NX53-efynCX
```
`Free  PE / Size       0 / 0`

**display logical volume**
```
$ sudo lvdisplay 
  --- Logical volume ---
  LV Path                /dev/ubuntu-vg/ubuntu-lv
  LV Name                ubuntu-lv
  VG Name                ubuntu-vg
  LV UUID                xP6AsV-2A5E-npbO-LV5v-1DoJ-mZZ7-gIgYCO
  LV Write Access        read/write
  LV Creation host, time ubuntu-server, 2023-07-05 07:44:40 +0000
  LV Status              available
  # open                 1
  LV Size                265.69 GiB
  Current LE             68017
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/ubuntu-vg/lxd-lv
  LV Name                lxd-lv
  VG Name                ubuntu-vg
  LV UUID                IYRNHl-JZ73-Mwp4-kU6F-Nn2x-ortt-gEUese
  LV Write Access        read/write
  LV Creation host, time server03, 2023-07-12 07:08:58 +0000
  LV Status              available
  # open                 0
  LV Size                <662.77 GiB
  Current LE             169668
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```


### [Creating Filesystems](https://linuxhint.com/lvm-how-to-create-logical-volumes-and-filesystems/)

After creating the logical volumes, now the final step is to create a filesystem on top of the logical volume.

### Reference
* [Use LVM To Manage Storage Devices on Ubuntu ](https://www.digitalocean.com/community/tutorials/how-to-use-lvm-to-manage-storage-devices-on-ubuntu-18-04)

* [LVM: How to Create Logical Volumes](https://linuxhint.com/lvm-how-to-create-logical-volumes-and-filesystems/)