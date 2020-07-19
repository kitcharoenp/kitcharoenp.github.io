---
layout: post
title: "ProxySQL2: Replication hostgroups"
categories: [mysql]
---
> **ProxySQL** checks the value of `read_only`  for servers configured in `mysql_replication_hostgroups` table. With this table, the listed hostgroups can be configured in **pairs of writer and reader hostgroups**.[1][1]

### Show `mysql_replication_hostgroups`

```sql
proxy_admin> SELECT * FROM mysql_replication_hostgroups;
Empty set (0.00 sec)
```

### Insert Read/Write hostgroup

```sql
proxy_admin> INSERT INTO mysql_replication_hostgroups VALUES (1,2,'read_only|innodb_read_only','cluster1');
```

```sql
proxy_admin> SELECT * FROM mysql_replication_hostgroups;
+------------------+------------------+----------------------------+----------+
| writer_hostgroup | reader_hostgroup | check_type                 | comment  |
+------------------+------------------+----------------------------+----------+
| 1                | 2                | read_only|innodb_read_only | cluster1 |
+------------------+------------------+----------------------------+----------+
```
> Now, all the servers that are either configured in hostgroup 1 or 2 will be moved to the correct hostgroup:
> * If servers have `read_only`=0 , they will be moved to hostgroup 1
> * If servers have `read_only`=1 , they will be moved to hostgroup 2

### Servers Status

```sql
proxy_admin> SELECT hostgroup_id, hostname, port, status FROM mysql_servers;
+--------------+--------------+------+--------+
| hostgroup_id | hostname     | port | status |
+--------------+--------------+------+--------+
| 1            | 10.10.10.22  | 3306 | ONLINE |
| 1            | 10.10.10.210 | 3306 | ONLINE |
+--------------+--------------+------+--------+
```
### Load Servers to Runtime

```sql
proxy_admin> LOAD MYSQL SERVERS TO RUNTIME;
Query OK, 0 rows affected (0.00 sec)

proxy_admin> SELECT hostgroup_id, hostname, port, status FROM mysql_servers;
+--------------+--------------+------+--------+
| hostgroup_id | hostname     | port | status |
+--------------+--------------+------+--------+
| 1            | 10.10.10.22  | 3306 | ONLINE |
| 2            | 10.10.10.210 | 3306 | ONLINE |
+--------------+--------------+------+--------+
2 rows in set (0.00 sec)
```

### Save the configuration to disk

```sql
proxy_admin> SAVE MYSQL SERVERS TO DISK;
Query OK, 0 rows affected (0.13 sec)

proxy_admin> SAVE MYSQL VARIABLES TO DISK;
Query OK, 134 rows affected (0.04 sec)
```

[1]: https://proxysql.com/documentation/ProxySQL-Configuration/ "ProxySQL-Configuration"
