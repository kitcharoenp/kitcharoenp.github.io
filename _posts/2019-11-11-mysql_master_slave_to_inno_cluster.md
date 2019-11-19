---
layout: post
title: "MySQL : Master-Slave to InnoDB Cluster"
published: true
categories: [mysql]
---
Notes while reading :

* [Migration from MySQL Master-Slave pair to MySQL InnoDB Cluster](https://lefred.be/content/migration-from-mysql-master-slave-pair-to-mysql-innodb-cluster-howto/)
* [Migrate from a single MySQL Instance to MySQL InnoDB Cluster using CLONE plugin](https://lefred.be/content/migrate-from-a-single-mysql-instance-to-mysql-innodb-cluster-using-clone-plugin/)
> Procedure :
> * Replication from current system
> * Creation of the cluster with a single instance (using MySQL Shell)
> * Adding instances to the cluster
> * Configure the router
> * Test phase
> * Pointing the application to the new solution

### Environment
The application connects to `mysql-repl` which also acts as master.
```
10.254.162.249 mysql-repl # mysql 8.0.18 + Clone Plugin

# InnoDB Cluster
10.254.162.31 mysqlv8-1a # mysql 8.0.18 + Clone Plugin
10.254.162.32 mysqlv8-1b # mysql 8.0.18 + Clone Plugin
10.254.162.33 mysqlv8-1c # mysql 8.0.18 + Clone Plugin
```
The `mysqlv8-1a`, `mysqlv8-1b` and `mysqlv8-1c` will be used for the new MySQL InnoDB Cluster.

### Prerequisite

#### [Install MySQL Server 8.0 and MySQL Shell](/mysql/2019/11/10/install_mysql_8.html)

#### [Configuring Hostname](https://dev.mysql.com/doc/refman/8.0/en/mysql-innodb-cluster-production-deployment.html)
> The instances which make up a cluster run on separate machines, therefore each machine must have a unique host name and be able to resolve the host names of the other machines which run server instances in the cluster.

To avoid DNS issues. Append below content to the end of the `/etc/hosts` file:
```
10.254.162.249 mysql-repl

# InnoDB Cluster
10.254.162.31 mysqlv8-1a
10.254.162.32 mysqlv8-1b
10.254.162.33 mysqlv8-1c
```
InnoDB cluster supports using IP addresses instead of host names.

#### Enable GTID / Clone Plugin
> The [clone plugin](https://dev.mysql.com/doc/refman/8.0/en/clone-plugin.html) permits cloning data locally or from a remote MySQL server instance. Cloned data is a physical snapshot of data stored in InnoDB that includes schemas, tables, tablespaces, and data dictionary metadata.
[To load the plugin at server startup](https://dev.mysql.com/doc/refman/8.0/en/clone-plugin-installation.html), use the `--plugin-load-add` option.

Manually editing a configuration file
```
$ sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```

append configuration file with
```
# The ID of each server instance within a cluster must be unique;

server-id = 249 #  Unique in each server

# Custom config

# Enable GTID
enforce_gtid_consistency=1
gtid_mode=on

# Load the `Clone Plugin` at server startup
plugin-load-add=mysql_clone.so

```

Then restart MySQL to make the change. For more information please read [Replication with Global Transaction Identifiers](https://dev.mysql.com/doc/refman/8.0/en/replication-gtids.html)

#### Check `GTID`
single:
```
mysql-repl> select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          249 | ON          | ON                         |
+-------------+-------------+----------------------------+
```
mysqlv8-1a:
```
mysqlv8-1a> select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          31 | ON          | ON                         |
+-------------+-------------+----------------------------+
```

mysqlv8-1b:
```
mysqlv8-1b> select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          32 | ON          | ON                         |
+-------------+-------------+----------------------------+
```

#### Check `Clone plugin`
```sql
mysql> SELECT PLUGIN_NAME, PLUGIN_STATUS
       FROM INFORMATION_SCHEMA.PLUGINS
       WHERE PLUGIN_NAME = 'clone';
+------------------------+---------------+
| PLUGIN_NAME            | PLUGIN_STATUS |
+------------------------+---------------+
| clone                  | ACTIVE        |
+------------------------+---------------+
```

#### Clone instance
Clone instance :  `mysql-repl` to `mysqlv8-1a`
* Clone `mysql-repl` instance:
```
mysqlv8-1a> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.0385 sec
mysqlv8-1a> clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
ERROR: 3869 (HY000): Clone system configuration: mysql-repl:3306 is not found in clone_valid_donor_list:
mysqlv8-1a>
```

* Set `clone_valid_donor_list`:
```
mysqlv8-1a> set GLOBAL clone_valid_donor_list='mysql-repl:3306';
Smysqlv8-1a> clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
ERROR: 3862: Clone Donor Error: Connect failed: 1045 : Access denied for user 'repl'@'mysqlv8-1a' (using password: YES).
```

* Create replication user and assigns privileges:
```
mysql-repl> CREATE USER 'repl'@'mysqlv8%' IDENTIFIED BY 'repl_password' REQUIRE SSL;
mysql-repl> GRANT REPLICATION SLAVE, BACKUP_ADMIN, CLONE_ADMIN ON *.* TO 'repl'@'mysqlv8%';
```

* Try again:
```
mysqlv8-1a> clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
Query OK, 0 rows affected (8 min 41.1291 sec)
...
mysqlv8-1a> show databases;
ERROR: 2006: MySQL server has gone away
The global session got disconnected..
Attempting to reconnect to 'mysqlx://root@localhost:33060'..
The global session was successfully reconnected.
SQL > show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| phpmyadmin         |
| mydb2              |
| sys                |
| mydb1              |
+--------------------+
7 rows in set (0.1035 sec)
```
* Show clone status and progress:
```
mysqlv8-1a> use performance_schema;
mysqlv8-1a> select * from clone_progress;
+----+-----------+-----------+----------------------------+----------------------------+---------+-------------+-------------+-------------+------------+---------------+
| ID | STAGE     | STATE     | BEGIN_TIME                 | END_TIME                   | THREADS | ESTIMATE    | DATA        | NETWORK     | DATA_SPEED | NETWORK_SPEED |
+----+-----------+-----------+----------------------------+----------------------------+---------+-------------+-------------+-------------+------------+---------------+
|  1 | DROP DATA | Completed | 2019-11-19 08:32:52.500749 | 2019-11-19 08:32:57.294793 |       1 |           0 |           0 |           0 |          0 |             0 |
|  1 | FILE COPY | Completed | 2019-11-19 08:32:57.295147 | 2019-11-19 08:41:28.702525 |       2 | 11015394279 | 11015394279 | 11016028459 |          0 |             0 |
|  1 | PAGE COPY | Completed | 2019-11-19 08:41:28.702649 | 2019-11-19 08:41:31.157693 |       2 |    10698752 |    10698752 |    10735517 |          0 |             0 |
|  1 | REDO COPY | Completed | 2019-11-19 08:41:31.157971 | 2019-11-19 08:41:31.471893 |       2 |     2290688 |     2290688 |     2291295 |          0 |             0 |
|  1 | FILE SYNC | Completed | 2019-11-19 08:41:31.472091 | 2019-11-19 08:41:33.62927  |       2 |           0 |           0 |           0 |          0 |             0 |
|  1 | RESTART   | Completed | 2019-11-19 08:41:33.62927  | 2019-11-19 08:42:04.802818 |       0 |           0 |           0 |           0 |          0 |             0 |
|  1 | RECOVERY  | Completed | 2019-11-19 08:42:04.802818 | 2019-11-19 08:42:19.703579 |       0 |           0 |           0 |           0 |          0 |             0 |
+----+-----------+-----------+----------------------------+----------------------------+---------+-------------+-------------+-------------+------------+---------------+
7 rows in set (0.0025 sec)
...
mysqlv8-1a>  select ID, STATE, ERROR_MESSAGE, BINLOG_POSITION from clone_status;
+----+-----------+---------------+-----------------+
| ID | STATE     | ERROR_MESSAGE | BINLOG_POSITION |
+----+-----------+---------------+-----------------+
|  1 | Completed |               |             742 |
+----+-----------+---------------+-----------------+
1 row in set (0.0004 sec)
```

### MySQL InnoDB Cluster – howto install it from scratch

Get a temporay password from mysqld.log after fresh installation mysql
```
grep passwd /var/log/mysqld.log
```
