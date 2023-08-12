---
layout : post
title : "LXD Troubleshoot"
categories : [ubuntu, lxd]
published : false
---


* `level=warning msg=" - Couldn't find the CGroup network priority controller, network priority will be ignored"`

* `level=warning msg="Failed getting exec control websocket reader, killing command" PID=13333 err="read unix /var/snap/lxd/common/lxd/unix.socket->@: read: connection reset by peer" `


Btrfs storage pool | containter can not stop | container cannot get ip address
```
lxc KfinViz-A0 20230808021933.625 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3621 - newuidmap binary is missing
lxc KfinViz-A0 20230808021933.625 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3627 - newgidmap binary is missing
```


Log:

lxc KfinViz-A0 20230809074833.569 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3621 - newuidmap binary is missing
lxc KfinViz-A0 20230809074833.569 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3627 - newgidmap binary is missing
lxc KfinViz-A0 20230809074833.570 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3621 - newuidmap binary is missing
lxc KfinViz-A0 20230809074833.570 WARN     conf - ../src/src/lxc/conf.c:lxc_map_ids:3627 - newgidmap binary is missin



  volatile.idmap.base: "0"
  volatile.idmap.current: '[{"Isuid":true,"Isgid":false,"Hostid":1000000,"Nsid":0,"Maprange":1000000000},{"Isuid":false,"Isgid":true,"Hostid":1000000,"Nsid":0,"Maprange":1000000000}]'
  volatile.idmap.next: '[{"Isuid":true,"Isgid":false,"Hostid":1000000,"Nsid":0,"Maprange":1000000000},{"Isuid":false,"Isgid":true,"Hostid":1000000,"Nsid":0,"Maprange":1000000000}]'
  volatile.last_state.idmap: '[{"Isuid":true,"Isgid":false,"Hostid":1000000,"Nsid":0,"Maprange":1000000000},{"Isuid":false,"Isgid":true,"Hostid":1000000,"Nsid":0,"Maprange":1000000000}]'


  ### [Solution](https://documentation.ubuntu.com/_/downloads/lxd/en/stable-4.0/pdf/)
  page10, 150 search for `newuidmap`

### LXD with CRIU btrfs

### Reference


### 
No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
N: Download is performed unsandboxed as root as file '/home/ubuntu/check-mk-free-2.1.0p2_0.jammy_amd64.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)

