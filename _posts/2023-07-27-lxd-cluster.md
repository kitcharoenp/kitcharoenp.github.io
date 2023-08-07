---
layout : post
title : "LXD Cluster"
categories : [ubuntu, lxd]
published : false
---




```
Error: Get "http://unix.socket/1.0": dial unix /var/snap/lxd/common/lxd/unix.socket: connect: connection refused


sudo apt install sqlite3


sudo sqlite3 /var/snap/lxd/common/lxd/database/local.db

sqlite> .tables
certificates  config        patches       raft_nodes    schema


sqlite> select * from schema
   ...> ;
1|42|1689933924
2|43|1690431075


sqlite> delete from schema where id =2;
sqlite> select * from schema;
1|42|1689933924


sqlite> select * from raft_nodes;
1|10.10.10.13:8443|0|server03
2|10.10.10.11:8443|1|server01

sqlite> delete from raft_nodes where id =2;
sqlite> select * from raft_nodes;
1|10.10.10.13:8443|0|server03


$ sudo snap start lxd

```

### Error: Error creating database: schema version '40' is more recent than expected '39'



### [Managing the LXD snap](https://discuss.linuxcontainers.org/t/managing-the-lxd-snap/8178)

LXD clusters must all run the same LXD version. 

https://discuss.linuxcontainers.org/t/snap-fix-channel/9725

snap switch lxd --channel=latest/stable

sudo snap refresh lxd


sudo snap install lxd --channel=latest/stable

snap refresh lxd --channel=latest/stable

To manually trigger a refresh on a cluster member and ensure that all cluster members are refreshing using the same phasing cohort, and thus get the same version, use:


sudo journalctl -u snap.lxd.daemon.service -f -n200

Production server:

* Use the latest/stable channel if you need the latest features and can specify a frequent refresh window.
* Use a snap refresh --hold lxd if you want to avoid the automatic release upgrade and have time to do manage the refresh cycle manually to ensure you get updates.
* Use an $LTS/stable channel if you don’t need any of the features that were added since the last LTS release but still want bug fixes and security updates.
* Set a refresh window to match your system maintenance window.


### Reference
* [Unable to complete LXD cluster node rename](https://discuss.linuxcontainers.org/t/unable-to-complete-lxd-cluster-node-rename/14723)

* [Can't lxc list, no unix.socket connection refused](https://github.com/canonical/lxd/issues/5423)

* [[SOLVED] LXD 3.18 Stopped working: unix.socket: connect: connection refused](https://discuss.linuxcontainers.org/t/solved-lxd-3-18-stopped-working-unix-socket-connect-connection-refused/6201)

* [Wrong scheme version after upgrade](https://github.com/canonical/lxd/issues/3465)

* [LXD Failed Cluster Upgrade - unable to update some nodes ](https://discuss.linuxcontainers.org/t/lxd-failed-cluster-upgrade-unable-to-update-some-nodes/7509)