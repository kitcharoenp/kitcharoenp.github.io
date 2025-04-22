---
layout : post
title : "Galera Cluster Crash Recovery"
categories : [mariadb, galera]
published : true
---

### All Nodes Go Down Without a Proper Shutdown Procedure

This scenario is possible in the case of a datacenter power failure or when hitting a MySQL or Galera bug. 


* Check each node and see which is the most advanced node. The node with the **highest sequence number** in its recovered position is the most up-to-date, and should be **chosen as bootstrap candidate**.

```shell
$ sudo cat /var/lib/mysql/grastate.dat
# GALERA saved state
version: 2.1
uuid:    b9f2a2b4-ffde-11ef-bcd1-a3710702a9fb
seqno:   -1
safe_to_bootstrap: 0
```

* In its `grastate.dat` file, set the `safe_to_bootstrap` variable to 1. Then, bootstrap from this node.

```
...
safe_to_bootstrap: 1
...
```

* To bootstrap the first node, invoke the startup script like this (For MariaDB):

```shell
$ sudo galera_new_cluster
```

### Reference
* [Crash Recovery](https://galeracluster.com/library/documentation/crash-recovery.html#crash-recovery)