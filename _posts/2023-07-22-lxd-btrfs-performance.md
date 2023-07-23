---
layout : post
title : "Create LXD clustering on btrfs"
categories : [lxd]
published : true
---

### btrfs


### Test on host (btrfs partition)

```shell
# dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 61.3417 s, 164 MB/s


# sync; echo 3 > /proc/sys/vm/drop_caches

# dd if=/root/input of=/root/test bs=128k count=75k conv=fdatasync
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 133.407 s, 75.5 MB/s
```

### Test on container (btrfs partition):
`crm1804a` is container name

```shell
# lxc exec crm1804a -- dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 67.6622 s, 149 MB/s

# sync; echo 3 > /proc/sys/vm/drop_caches

# lxc exec crm1804a -- dd if=/root/input of=/root/test bs=128k count=75k conv=fdatasync
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 150 s, 67.1 MB/s

```


### Test on host (lvm partition):
```shell
# dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 109.154 s, 92.2 MB/s


# sync; echo 3 > /proc/sys/vm/drop_caches

# dd if=/root/input of=/root/test bs=128k count=75k conv=fdatasync
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 133.407 s, 75.5 MB/s
```

### Test on container (zfs loopback):
`crm1804a` is container name

```shell
# lxc exec crm1804a -- dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 67.6622 s, 149 MB/s

# sync; echo 3 > /proc/sys/vm/drop_caches

# lxc exec crm1804a -- dd if=/root/input of=/root/test bs=128k count=75k conv=fdatasync
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 150 s, 67.1 MB/s

```


### Server01
```
# dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 71.8179 s, 140 MB/

# lxc exec container-test -- dd if=/dev/urandom of=/root/input bs=128k count=75k
76800+0 records in
76800+0 records out
10066329600 bytes (10 GB, 9.4 GiB) copied, 266.671 s, 37.7 MB/s
```