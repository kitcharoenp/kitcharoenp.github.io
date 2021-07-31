---
layout : post
title : "Enabling GTIDs for Server Replication in MariaDB"
categories : [mariadb]
published : true
---

MariaDB implements GTIDs differently from MySQL, making it possible to enable and disable them with no downtime. [\1\|][1]

On a master server, all updates to the database (DML and DDL) are written into the binary log as binlog events. A server can be both a primary and a replica at the same time, and it is thus possible for binlog events to replicated through multiple levels of servers. [\2\][2]

 A replica server keeps track of the position in the primary's binlog of the last event applied on the replica. **Global transaction ID** introduces a new event attached to each event group in the binlog. [\2\][2]

 ## Benefits
 *   Easy to change a replica server to connect to and replicate from a different primary server.

 *  The state of the replica is recorded in a crash-safe way.

The replica keeps track of its current position (the global transaction ID of the last transaction applied) in the **mysql.gtid_slave_pos** system table.  If this table is using a transactional storage engine (such as InnoDB, which is the default), then updates to the state are done in the same transaction as the updates to the data. This makes the state crash-safe; [\2\][2]


Global transaction ID integrates smoothly with old-style replication, and **the two can be used freely together** in the same replication hierarchy.  However, it must be explicitly **set for a replica server** with the appropriate **CHANGE MASTER** option;

**Simple replication setups only have a single primary** being updated by the application at any one time. In such setups, there is **only a single replication stream needed**. Then **domain ID can be ignored**, and left as the **default of 0** on all servers.


Replica will use global transaction ID, use the `CHANGE MASTER` `master_use_gtid` option:

```
CHANGE MASTER TO master_use_gtid = { slave_pos | current_pos | no }
```


### Switching An Existing Old-Style Replica To Use GTID.

When a replica connects to a primary using old-style binlog positions, and the **primary supports GTID** (i.e. is MariaDB 10.0.2 or later), then the **replica automatically downloads the GTID position at connect and updates it during replication.**

```sql
-- before using GTID
SHOW SLAVE STATUS\G
...
  Master_Server_Id: 10210
    Master_SSL_Crl:
Master_SSL_Crlpath:
        Using_Gtid: No
       Gtid_IO_Pos:
...
```

Thus, once a replica has connected to the GTID-aware primary at least once, it can be switched to using GTID **without any other actions needed;** This means that an existing replica previously configured and running can be changed to connect with GTID (to the same or a new master) simply with:

```sql
-- stop slave then enable using GTID
STOP SLAVE;
CHANGE MASTER TO master_use_gtid = slave_pos;
START SLAVE;

SHOW SLAVE STATUS\G
...
Using_Gtid: Slave_Pos
Gtid_IO_Pos: 0-10210-3
...
```

### [Setting up a New Replica From a Backup][3]

It is important that the position at which replication is started corresponds exactly to the state of the data at the point in time that the backup was taken.
```sql

SELECT BINLOG_GTID_POS("master-bin.000001", 600);

SET GLOBAL gtid_slave_pos = "0-1-2";
CHANGE MASTER TO master_host="127.0.0.1", master_port=3310, master_user="root", master_use_gtid=slave_pos;
START SLAVE;
```

[1]: https://mariadb.com/resources/blog/enabling-gtids-for-server-replication-in-mariadb-server-10-2/ "Enabling GTIDs MariaDB"

[2]: https://mariadb.com/kb/en/gtid/#the-domain-id "Global Transaction ID"

[3]: https://mariadb.com/kb/en/gtid/#setting-up-a-new-replica-from-a-backup "Setting up a New Replica From a Backup"
