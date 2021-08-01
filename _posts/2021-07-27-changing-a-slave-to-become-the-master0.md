---
layout : post
title : "Changing a Slave to Become the Master"
categories : [mariadb, replication]
published : true
---
A typical scenario of when this is useful is if you have **set up a new version of MariaDB as a slave**, for example for testing, and want to **upgrade your master** to the new version.

In MariaDB replication, a slave should be of a **version same or newer** than the master.  [_ref.][0]

### [Stopping the Original Master][0]
Manually Take Down the Master

```sql
-- set the master to read only to ensure that there are no new updates on the master
FLUSH TABLES WITH READ LOCK;

-- check the current position of the master:
SHOW MASTER STATUS;

+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000006 |      790 |              |                  |
+--------------------+----------+--------------+------------------+

-- When slave is up to date, you can then take the MASTER down.
SHUTDOWN;
```


### [Preparing the Slave to be a Master][1]

Wait until you have the same position on the slave. The most important information to watch are **Master_Log_File** and **Exec_Master_Log_Pos** as when this **matches the master**, it signals that all transactions has been committed on the slave.

```sql
SHOW SLAVE STATUS \G
...
                   Master_User: repl
                   Master_Port: 3306
                 Connect_Retry: 60
               Master_Log_File: mariadb-bin.000006
           Read_Master_Log_Pos: 790
                Relay_Log_File: mysqld-relay-bin.000010
                 Relay_Log_Pos: 557
         Relay_Master_Log_File: mariadb-bin.000006
              Slave_IO_Running: Yes
             Slave_SQL_Running: Yes
  ...
                    Last_Errno: 0
                    Last_Error:
                  Skip_Counter: 0
           Exec_Master_Log_Pos: 790 -- matches the master
               Relay_Log_Space: 867
               Until_Condition: None

```



Stop all old connections to the old master(s) and **reset read only mode**, if you had it enabled.

```sql
-- Stop all connections to master
STOP ALL SLAVES;
RESET SLAVE ALL;

-- reset read only mode,
SET @@global.read_only=0;
```

[0]: https://mariadb.com/kb/en/changing-a-slave-to-become-the-master/#stopping-the-original-master "Stopping the Original Master"

[1]: https://mariadb.com/kb/en/changing-a-slave-to-become-the-master/#preparing-the-slave-to-be-a-master "Preparing the Slave to be a Master"
