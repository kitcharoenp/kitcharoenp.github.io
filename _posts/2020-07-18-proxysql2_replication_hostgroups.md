---
layout: post
title: "ProxySQL2 Replication hostgroups"
categories: [mysql]
---
## MySQL replication hostgroups
> **ProxySQL** checks the value of `read_only`  for servers configured in `mysql_replication_hostgroups` table. With this table, the listed hostgroups can be configured in **pairs of writer and reader hostgroups**.[1][1]

### show `mysql_replication_hostgroups`

```sql
admin@127.0.0.1:[(none)]> SELECT * FROM mysql_replication_hostgroups;
Empty set (0.00 sec)
```

### insert server to `mysql_replication_hostgroups`

```sql
admin@127.0.0.1:[(none)]> INSERT INTO mysql_replication_hostgroups VALUES (1,2,'read_only|innodb_read_only','cluster1');
```

### show `mysql_replication_hostgroups`

```sql
admin@127.0.0.1:[(none)]> SELECT * FROM mysql_replication_hostgroups;
+------------------+------------------+----------------------------+----------+
| writer_hostgroup | reader_hostgroup | check_type                 | comment  |
+------------------+------------------+----------------------------+----------+
| 1                | 2                | read_only|innodb_read_only | cluster1 |
+------------------+------------------+----------------------------+----------+
```
> Now, all the servers that are either configured in hostgroup 1 or 2 will be moved to the correct hostgroup:
> * If servers have `read_only`=0 , they will be moved to hostgroup 1
> * If servers have `read_only`=1 , they will be moved to hostgroup 2

### servers status before load to runtime

```sql
admin@127.0.0.1:[(none)]> SELECT hostgroup_id, hostname, port, status FROM mysql_servers;
+--------------+--------------+------+--------+
| hostgroup_id | hostname     | port | status |
+--------------+--------------+------+--------+
| 1            | 10.10.10.22  | 3306 | ONLINE |
| 1            | 10.10.10.210 | 3306 | ONLINE |
+--------------+--------------+------+--------+
```
### load servers to runtime

```sql
admin@127.0.0.1:[(none)]> LOAD MYSQL SERVERS TO RUNTIME;
Query OK, 0 rows affected (0.00 sec)

admin@127.0.0.1:[(none)]> SELECT hostgroup_id, hostname, port, status FROM mysql_servers;
+--------------+--------------+------+--------+
| hostgroup_id | hostname     | port | status |
+--------------+--------------+------+--------+
| 1            | 10.10.10.22  | 3306 | ONLINE |
| 2            | 10.10.10.210 | 3306 | ONLINE |
+--------------+--------------+------+--------+
2 rows in set (0.00 sec)
```

### save the configuration to disk

```sql
admin@127.0.0.1:[(none)]> SAVE MYSQL SERVERS TO DISK;
Query OK, 0 rows affected (0.13 sec)

admin@127.0.0.1:[(none)]> SAVE MYSQL VARIABLES TO DISK;
Query OK, 134 rows affected (0.04 sec)
```

## MySQL Users
Configure the database users and then load configuration to runtime, and save it to disk to make it persistent across restart.

### The `mysql_users` is initially empty.

```sql
admin@127.0.0.1:[(none)]> SELECT * FROM mysql_users;
Empty set (0.00 sec)
```
* **try connect `proxysql[10.10.10.218]` to `backend servers`**

```shell
$ mysql -u backend_db_user -pbackend_db_passwd -h 10.10.10.218 -P6033 -e "SELECT 1"
ERROR 1045 (28000): ProxySQL Error: Access denied for user 'backend_db_user'@'10.10.10.20' (using password: YES)

```

### insert mysql users

```sql
admin@127.0.0.1:[(none)]> INSERT INTO mysql_users(username,password,default_hostgroup) VALUES ('backend_db_user','backend_db_passwd',1);
Query OK, 1 row affected (0.00 sec)

admin@127.0.0.1:[(none)]> SELECT * FROM mysql_users;
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+
| username | password  | active | use_ssl | default_hostgroup | default_schema | schema_locked | transaction_persistent | fast_forward | backend | frontend | max_connections | comment |
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+
| backend_db_user    | backend_db_passwd | 1      | 0       | 1                 | NULL           | 0             | 1                      | 0            | 1       | 1        | 10000           |         |
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+

admin@127.0.0.1:[(none)]> LOAD MYSQL USERS TO RUNTIME;
Query OK, 0 rows affected (0.00 sec)

```

### try connect `proxysql[10.10.10.218]` to `backend servers`

```shell
$ mysql -u backend_db_user -pbackend_db_passwd -h 10.10.10.218 -P6033 -e "SELECT 1"
+---+
| 1 |
+---+
| 1 |
+---+
$ mysql -u backend_db_user -pbackend_db_passwd -h 10.10.10.218 -P6033 -e "SELECT @@port"
+--------+
| @@port |
+--------+
|   3306 |
+--------+
```

[1]: https://proxysql.com/documentation/ProxySQL-Configuration/ "ProxySQL-Configuration"
