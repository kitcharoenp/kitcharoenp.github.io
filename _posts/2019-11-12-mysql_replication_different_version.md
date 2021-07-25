---
layout: post
title: "MySQL : Replication with different version"
published: true
categories: [mysql]
---
Notes while reading : [How to setup a slave for replication in 6 simple steps with Percona XtraBackup](https://www.alibabacloud.com/blog/how-to-configure-mysql-master-slave-replication-on-ubuntu-16-04_593982)

> ### [Replication Compatibility Between MySQL Versions](https://dev.mysql.com/doc/refman/8.0/en/replication-compatibility.html)
> MySQL supports replication from one release series to the next higher release series.For example, you can replicate from a master running MySQL 5.6 to a slave running MySQL 5.7, **from a master running MySQL 5.7 to a slave running MySQL 8.0**, and so on.

### Running MySQL Master/Slave
```
Master server: 10.254.162.159 (Percona XtraDB Cluster 5.7.26)
Slave server: 10.254.162.249 (Server version: 8.0.18 MySQL Community Server)
```

### Configuring Master Server

#### Retrieve the current version
From within the MySQL client
```
mysql> SHOW VARIABLES LIKE "%version%";
+-------------------------+-------------------------------------------------------------------------------------------------+
| Variable_name           | Value                                                                                           |
+-------------------------+-------------------------------------------------------------------------------------------------+
| innodb_version          | 5.7.26-29                                                                                       |
| protocol_version        | 10                                                                                              |
| slave_type_conversions  |                                                                                                 |
| tls_version             | TLSv1,TLSv1.1,TLSv1.2                                                                           |
| version                 | 5.7.26-29-57-log                                                                                |
| version_comment         | Percona XtraDB Cluster (GPL), Release rel29, Revision 03540a3, WSREP version 31.37, wsrep_31.37 |
| version_compile_machine | x86_64                                                                                          |
| version_compile_os      | debian-linux-gnu                                                                                |
| version_suffix          | -57-log                                                                                         |
+-------------------------+-------------------------------------------------------------------------------------------------+
9 rows in set (0.06 sec)
```

#### [Installing Percona XtraBackup](https://www.percona.com/doc/percona-xtrabackup/2.4/installation/apt_repo.html)
> Percona XtraBackup is an open-source hot backup utility for MySQL - based servers that doesn’t lock your database during the backup.

### Make a backup on Master and prepare it
* Create a `/opt/perconaXtraBackup` directory for backup the Master MySQL data
```
# mkdir -p /opt/perconaXtraBackup
```
* Make a backup with `xtrabackup`. This will make a copy of your MySQL data dir to the `/opt/perconaXtraBackup` directory
```
# xtrabackup --user=root --password=mysqlrootpassword /opt/perconaXtraBackup/
...
innobackupex: completed OK!
```
* show data in backup directory
```
# ls /opt/perconaXtraBackup/
2019-11-12_08-50-30
# ls /opt/perconaXtraBackup/2019-11-12_08-50-30/
backup-my.cnf   ibtmp1              sys                           xtrabackup_info
ib_buffer_pool  mysql               xtrabackup_binlog_info        xtrabackup_logfile
ib_logfile0     performance_schema  xtrabackup_binlog_pos_innodb  xtrabackup_master_key_id
ib_logfile1     phpmyadmin          xtrabackup_checkpoints        mydb1
ibdata1         mydb2               xtrabackup_galera_info
```
* Prepare the data
```
# xtrabackup --user=root --password=mysqlrootpassword --apply-log /opt/perconaXtraBackup/2019-11-12_08-50-30/
...
InnoDB: Shutdown completed; log sequence number 39984943813
160128 12:41:38 completed OK
```

#### Create a new user for replication
```
mysql> CREATE USER 'repl'@'10.254.162.249' IDENTIFIED BY 'repl_password';
```

### Change password
```mysql
mysql> ALTER USER 'repl'@'10.254.162.249' IDENTIFIED BY 'repl_password';
mysql> FLUSH PRIVILEGES;
```

#### Grant access to the replication slave
```
 mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'10.254.162.249';
 mysql> FLUSH PRIVILEGES;
 ...
 mysql> SHOW GRANTS FOR 'repl'@'10.254.162.249';
+---------------------------------------------------------+
| Grants for repl@10.254.162.249                            |
+---------------------------------------------------------+
| GRANT REPLICATION SLAVE ON *.* TO 'repl'@'10.254.162.249' |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

#### Rsync data to slave
Use `rsync` to copy the data from master to slave
```
# rsync -avpP -e ssh /opt/perconaXtraBackup/2019-11-12_08-50-30/ \
ubuntu@10.254.162.249:/opt/perconaXtraBackup/
```

### Configuration Slave Server
#### Modify the configuration file.
Specify a value for the `server-id` attribute in the `[mysqld]` section.
```
$ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
...
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
datadir		= /var/lib/mysql
log-error	= /var/log/mysql/error.log

# My custom config
server-id = 21

enforce_gtid_consistency=1
gtid_mode=on
```

#### Stop service
```
$ sudo service mysql stop
```

#### Back up
Back up the original datadir or previously installed MySQL datadir `/var/lib/mysql`
```
$ sudo mkdir /tmp/mysql
$ sudo mv /var/lib/mysql/* /tmp/mysql/
```

#### Move snapshot
```
$ sudo mv /opt/perconaXtraBackup/2019-11-12_08-50-30/* /var/lib/mysql/
```
or use xtrabackup
```
$ sudo xtrabackup --move-back --target-dir=/opt/perconaXtraBackup/2019-11-12_08-50-30
```
#### Change `datadir` permissions
```
$ sudo  chown mysql:mysql /var/lib/mysql
```

#### Start service
```
$ sudo service mysql start
```

#### Verify Log
```
2019-11-12T10:03:07.872789Z 0 [Warning] [MY-010075] [Server] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: a0476bcc-0533-11ea-979a-00163e208658.
2019-11-12T10:03:08.112649Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2019-11-12T10:03:08.161845Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libfnv1a_udf.so' (errno: 33 /usr/lib/mysql/plugin/libfnv1a_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.162441Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libfnv_udf.so' (errno: 33 /usr/lib/mysql/plugin/libfnv_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.162988Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libmurmur_udf.so' (errno: 33 /usr/lib/mysql/plugin/libmurmur_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.170098Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.18'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
2019-11-12T10:03:08.322168Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
```
This error cause from [Percona Toolkit UDFs](https://www.percona.com/doc/percona-server/LATEST/management/udf_percona_toolkit.html) because we dump d

#### Test connect `master` from `slave`
```
$ mysql --host=10.254.162.159 --user=repl --password=repl_password
```

#### Finding the binary log position
```
$ sudo cat /var/lib/mysql/xtrabackup_binlog_info
master-bin.000119	1326
```

#### Configure the slave to replicate from the Master server
**master_log_file** : `master-bin.000119` , **master_log_pos** : `1326`

```
mysql> STOP SLAVE;

mysql> CHANGE MASTER TO
    MASTER_HOST ='10.254.162.159',
    MASTER_USER ='repl',
    MASTER_PASSWORD ='repl_password',
    MASTER_LOG_FILE = 'master-bin.000119',
    MASTER_LOG_POS = 1326;

mysql> START SLAVE;
```

#### Check slave
```
mysql> SHOW SLAVE STATUS \G
         ...
         Slave_IO_Running: Yes
         Slave_SQL_Running: Yes
         ...
```
view mysql log on `slave`
```
$ tail -f -n 200 /var/log/mysql/error.log
2019-11-15T07:24:41.655603Z 13 [System] [MY-010562] [Repl] Slave I/O thread for channel '': connected to master 'repl@10.254.162.159:3306',replication started in log 'FIRST' at position 4
```

### Mysql command
#### Show users
```
mysql> SELECT user, host FROM mysql.user;
+------------------+--------------+
| user             | host         |
+------------------+--------------+
...
| proxysql_monitor | %            |
| root             | %            |
| phpmyadmin       | %            |
| repl             | %            |
...
```

#### Show grants
```
mysql> SHOW GRANTS FOR 'user-name-here'@'host';
```

#### Changing host permissions
```
UPDATE mysql.user SET host='new_host' WHERE host='current_host' AND user='user-name-here';
FLUSH PRIVILEGES;
```

#### Change user password
```
ALTER USER 'user-name-here'@'hostname' IDENTIFIED BY 'new-password';

# version < 5.7.5
SET PASSWORD FOR 'user-name-here'@'hostname' = PASSWORD('new-password');
```
