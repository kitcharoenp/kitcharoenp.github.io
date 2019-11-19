---
layout: post
title: "MySQL : InnoDB Cluster"
published: true
categories: [mysql]
---
Notes while reading :

> ### [Migration from MySQL Master-Slave pair to MySQL InnoDB Cluster: howto](https://lefred.be/content/migration-from-mysql-master-slave-pair-to-mysql-innodb-cluster-howto/)
> * replication from current system
> * creation of the cluster with a single instance (using MySQL Shell)
> * adding instances to the cluster
> * configure the router
> * test phase
> * pointing the application to the new solution

### Provision
```
single : 10.254.162.249/24

# InnoDB Cluster
mysqlv8-1a : 10.254.162.31/24
mysqlv8-1b : 10.254.162.32/24
mysqlv8-1c : 10.254.162.33/24
```

#### Config hostname
Edit `/etc/hosts` append with:
```
single 10.254.162.249

# InnoDB Cluster
mysqlv8-1a 10.254.162.31
mysqlv8-1b 10.254.162.32
mysqlv8-1c 10.254.162.33
```

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

#### Verify `GTID` enabled
```
single> select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          249 | ON          | ON                         |
+-------------+-------------+----------------------------+
1 row in set (0.00 sec)

single>
```

#### Verify `Clone plugin` installation
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

#### Clone
```
clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
```


#### Replication User
```
single> CREATE USER 'repl'@'10.254.162.21' IDENTIFIED BY 'repl_password' REQUIRE SSL;
single> GRANT REPLICATION SLAVE, BACKUP_ADMIN, CLONE_ADMIN ON *.* TO 'repl'@'10.254.162.21';
```



### MySQL InnoDB Cluster – howto install it from scratch

Get a temporay password from mysqld.log after fresh installation mysql
```
grep passwd /var/log/mysqld.log
```
