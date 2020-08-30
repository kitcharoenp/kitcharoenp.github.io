---
layout: post
title: "ProxySQL2: Backend Users Config"
categories: [mysql]
---

Configure the database users and then load configuration to runtime, and save it to disk to make it persistent across restart.

### The `mysql_users` is initially empty.

```sql
proxy_admin> SELECT * FROM mysql_users;
Empty set (0.00 sec)
```
### Try connect `proxysql[10.10.10.218]` to `backend servers`

```shell
$ mysql -u backend_db_user -pbackend_db_passwd -h 10.10.10.218 -P6033 -e "SELECT 1"
ERROR 1045 (28000): ProxySQL Error: Access denied for user 'backend_db_user'@'10.10.10.20' (using password: YES)

```

### Insert `mysql_users`

```sql
proxy_admin> INSERT INTO mysql_users(username,password,default_hostgroup) VALUES ('backend_db_user','backend_db_passwd',1);
Query OK, 1 row affected (0.00 sec)

proxy_admin> SELECT * FROM mysql_users;
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+
| username | password  | active | use_ssl | default_hostgroup | default_schema | schema_locked | transaction_persistent | fast_forward | backend | frontend | max_connections | comment |
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+
| backend_db_user    | backend_db_passwd | 1      | 0       | 1                 | NULL           | 0             | 1                      | 0            | 1       | 1        | 10000           |         |
+----------+-----------+--------+---------+-------------------+----------------+---------------+------------------------+--------------+---------+----------+-----------------+---------+

proxy_admin> LOAD MYSQL USERS TO RUNTIME;
Query OK, 0 rows affected (0.00 sec)

```

### Try reconnect `proxysql[10.10.10.218]` to `backend servers`

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

```sql
proxy_admin> SAVE MYSQL USERS TO DISK;
Query OK, 0 rows affected (0.00 sec)
```

[1]: https://proxysql.com/documentation/ProxySQL-Configuration/ "ProxySQL-Configuration"
