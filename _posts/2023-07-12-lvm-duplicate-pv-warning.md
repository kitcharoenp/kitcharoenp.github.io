---
layout : post
title : "Duplicate PV Warnings for Multipathed Devices"
categories : [lvm, hpe]
published : true
---
> Without [DM Multipath]((https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/dm_multipath/mpath_devices)), each path from a server node to a storage controller is treated by the system as a separate device, even when the I/O path connects the same server node to the same storage controller. DM Multipath provides a way of organizing the I/O paths logically, by creating a single multipath device on top of the underlying devices.


> When using Device Mapper Multipath or other multipath software such as EMC PowerPath or Hitachi Dynamic Link Manager (HDLM), each path to a particular logical unit number (LUN) is registered as a different SCSI device, such as /dev/sdb or /dev/sdc. 
Display messages

```shell
# vgscan 
  WARNING: Not using device /dev/sdb3 for PV eKWtzc-CvWO-3qqf-dwR2-VnlH-pz7u-2uYKWk.
  WARNING: PV eKWtzc-CvWO-3qqf-dwR2-VnlH-pz7u-2uYKWk prefers device /dev/sda3 because device is used by LV.
  Found volume group "ubuntu-vg" using metadata type lvm2
```
> * The two devices displayed in the output are both single paths to the same device 
> * The two devices displayed in the output are both multipath maps 

### [Solution](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/duplicate_pv_multipath)

 Before update `filter` in `/etc/lvm/lvm.conf`. The Output command `pvscan` is :
```shell
$ sudo pvscan 
  WARNING: Not using device /dev/sdb3 for PV eKWtzc-CvWO-3qqf-dwR2-VnlH-pz7u-2uYKWk.
  WARNING: PV eKWtzc-CvWO-3qqf-dwR2-VnlH-pz7u-2uYKWk prefers device /dev/sda3 because device is used by LV.
  PV /dev/sda3   VG ubuntu-vg       lvm2 [<928.46 GiB / <828.46 GiB free]
  Total: 1 [<928.46 GiB] / in use: 1 [<928.46 GiB] / in no VG: 0 [0   ]
$
```

To prevent this warning from appearing, you can configure a filter in the `/etc/lvm/lvm.conf` file to restrict the devices that LVM will search for metadata.

```shell
# This filter accepts the third partition on the first hard drive, while rejecting everything else. 
filter = [ "a|/dev/sda3$|", "r|.*|" ]
```


 After update `filter` in `/etc/lvm/lvm.conf`

 ```shell
 $ sudo pvscan 
  PV /dev/sda3   VG ubuntu-vg       lvm2 [<928.46 GiB / <828.46 GiB free]
  Total: 1 [<928.46 GiB] / in use: 1 [<928.46 GiB] / in no VG: 0 [0   ]

 ```


### Reference
* [Duplicate PV Warnings for Multipathed Devices](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/logical_volume_manager_administration/duplicate_pv_multipath)