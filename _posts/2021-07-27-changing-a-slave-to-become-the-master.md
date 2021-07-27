---
layout : post
title : "Changing a Slave to Become the Master"
categories : [mariadb, replication]
published : true
---

### [Stopping the Original Master][0]
Manually Take Down the Master

```sql
-- set the master to read only to ensure that there are no new updates on the master
FLUSH TABLES WITH READ LOCK;

-- check the current position of the master:
SHOW MASTER STATUS;

SELECT @@global.gtid_binlog_pos;

SHOW SLAVE [connection_name] STATUS;

-- When slave is up to date, you can then take the MASTER down.
SHUTDOWN;
```


### [Preparing the Slave to be a Master][1]
Stop all old connections to the old master(s) and **reset read only mode**, if you had it enabled.
```sql
STOP ALL SLAVES;
RESET SLAVE ALL;
SHOW MASTER STATUS;
SELECT @@global.gtid_binlog_pos;
SET @@global.read_only=0;
```

[0]: https://mariadb.com/kb/en/changing-a-slave-to-become-the-master/#stopping-the-original-master "Stopping the Original Master"

[1]: https://mariadb.com/kb/en/changing-a-slave-to-become-the-master/#preparing-the-slave-to-be-a-master "Preparing the Slave to be a Master"
