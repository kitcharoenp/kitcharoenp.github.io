---
layout: post
title: "MySQL Master-Slave Replication"
published: false
categories: [mysql]
---
Notes while reading : [How to Configure MySQL Master-Slave Replication on Ubuntu 16.04](https://www.alibabacloud.com/blog/how-to-configure-mysql-master-slave-replication-on-ubuntu-16-04_593982)


### Prerequisites
```
Master server: 10.254.162.159
Slave server: 10.254.162.249
```

### Configuring Master Server
* Create a New User for Replication
```
mysql> CREATE USER 'repl'@'10.254.162.249' IDENTIFIED BY 'repl_password';
```

* Grant access to the replication slave
```
 mysql> GRANT REPLICATION SLAVE ON *.*TO 'repl'@'10.254.162.249';
 ...
 mysql> SHOW GRANTS FOR 'repl'@'10.254.162.249';
+---------------------------------------------------------+
| Grants for repl@10.10.10.210                            |
+---------------------------------------------------------+
| GRANT REPLICATION SLAVE ON *.* TO 'repl'@'10.254.162.249' |
+---------------------------------------------------------+
1 row in set (0.00 sec)
```

* Show Master status
```
mysql> show master status;
+---------------+----------+--------------+------------------+-------------------+
| File          | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+---------------+----------+--------------+------------------+-------------------+
| binlog.000005 |      155 |              |                  |                   |
+---------------+----------+--------------+------------------+-------------------+
```



### Configuring Slave Server
* dump data from master
* verify mysql
```
2019-11-12T10:03:07.872789Z 0 [Warning] [MY-010075] [Server] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: a0476bcc-0533-11ea-979a-00163e208658.
2019-11-12T10:03:08.112649Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2019-11-12T10:03:08.161845Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libfnv1a_udf.so' (errno: 33 /usr/lib/mysql/plugin/libfnv1a_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.162441Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libfnv_udf.so' (errno: 33 /usr/lib/mysql/plugin/libfnv_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.162988Z 0 [ERROR] [MY-010901] [Server] Can't open shared library 'libmurmur_udf.so' (errno: 33 /usr/lib/mysql/plugin/libmurmur_udf.so: cannot open shared object file: No such file or directory).
2019-11-12T10:03:08.170098Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.18'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
2019-11-12T10:03:08.322168Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Socket: '/var/run/mysqld/mysqlx.sock' bind-address: '::' port: 33060
```


* Modify the configuration file, specify a value for the `server-id` attribute in the `[mysqld]` section.
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

* Restart Slave Server
```
$ sudo systemctl restart mysql
```

* Configure the slave to replicate from the Master server
    - master_log_file and  : result from command `show master status` on master
    - master_log_pos : result from command `show master status` on master
```
mysql> STOP SLAVE;

mysql> CHANGE MASTER TO MASTER_HOST ='master_ip_address', MASTER_USER ='replication_user', MASTER_PASSWORD ='replication_password', MASTER_LOG_FILE = 'master_log_file', MASTER_LOG_POS = master_log_pos;

mysql> START SLAVE;
```



### Show users
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

### Show grants
```
mysql> SHOW GRANTS FOR 'user-name-here'@'host';
```

### Changing host permissions
```
UPDATE mysql.user SET host='new_host' WHERE host='current_host' AND user='user-name-here';
FLUSH PRIVILEGES;
```

### Change user password
```
ALTER USER 'user-name-here'@'hostname' IDENTIFIED BY 'new-password';

# version < 5.7.5
SET PASSWORD FOR 'user-name-here'@'hostname' = PASSWORD('new-password');
```
