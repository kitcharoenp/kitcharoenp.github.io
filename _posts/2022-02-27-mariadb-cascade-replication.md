---
layout : post
title : "MariaDB Cascade Replication"
categories : [mariadb, replication]
published : true
---

[Daisy Chain Replication](https://www.reddit.com/r/mariadb/comments/qcyub6/can_i_daisy_chain_replication_dbhost1dbhost2/)

```config
# Daisy Chain Replication
log_bin=ON
binlog_format=MIXED
innodb_autoinc_lock_mode=2
log_slave_updates=ON
relay_log_purge=ON
relay_log_recovery=ON
```

* **[`innodb_autoinc_lock_mode`](https://mariadb.com/kb/en/auto_increment-handling-in-innodb/)** When `innodb_autoinc_lock_mode` is set to 2, InnoDB uses the **interleaved lock mode**. In this mode, InnoDB does not hold any table-level locks at all. This is the fastest and most scalable mode, but is not safe for statement-based replication.
* **`log_slave_updates`** Set to 1 if you want to daisy-chain the slaves.
* **[`binlog_format`](https://mariadb.com/kb/en/replication-and-binary-log-system-variables/#binlog_row_image)** Determines whether replication is row-based, statement-based or mixed. `binlog_format` only applies to normal (not replicated) updates.
* **[`relay_log_recovery`](https://mariadb.com/kb/en/replication-and-binary-log-system-variables/#relay_log_recovery)** Can be useful after the replica has crashed to prevent the processing of corrupt relay logs. relay_log_recovery should always be set together with `relay_log_purge`.  Setting `relay-log-recovery=1` with `relay-log-purge=0` can cause the relay log to be read from files that were not purged, leading to data inconsistencies.

