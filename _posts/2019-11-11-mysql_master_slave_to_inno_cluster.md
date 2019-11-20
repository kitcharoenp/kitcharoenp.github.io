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
mysql-repl > select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          249 | ON          | ON                         |
+-------------+-------------+----------------------------+
```
mysqlv8-1a:
```
mysqlv8-1a > select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
+-------------+-------------+----------------------------+
| @@server_id | @@gtid_mode | @@enforce_gtid_consistency |
+-------------+-------------+----------------------------+
|          31 | ON          | ON                         |
+-------------+-------------+----------------------------+
```

mysqlv8-1b:
```
mysqlv8-1b > select @@server_id,@@gtid_mode,@@enforce_gtid_consistency;
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
mysqlv8-1a > show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.0385 sec
mysqlv8-1a > clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
ERROR: 3869 (HY000): Clone system configuration: mysql-repl:3306 is not found in clone_valid_donor_list:
mysqlv8-1a>
```

* Set `clone_valid_donor_list`:
```
mysqlv8-1a > set GLOBAL clone_valid_donor_list='mysql-repl:3306';
mysqlv8-1a > clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
ERROR: 3862: Clone Donor Error: Connect failed: 1045 : Access denied for user 'repl'@'mysqlv8-1a' (using password: YES).
```

* Create replication user and assigns privileges:
```
mysql-repl > CREATE USER 'repl'@'mysqlv8%' IDENTIFIED BY 'repl_password' REQUIRE SSL;
mysql-repl > GRANT REPLICATION SLAVE, BACKUP_ADMIN, CLONE_ADMIN ON *.* TO 'repl'@'mysqlv8%';
```

* Try again:
```
mysqlv8-1a > clone instance from repl@'mysql-repl':3306 IDENTIFIED by 'repl_password';
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
mysqlv8-1a > use performance_schema;
mysqlv8-1a > select * from clone_progress;
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
mysqlv8-1a >  select ID, STATE, ERROR_MESSAGE, BINLOG_POSITION from clone_status;
+----+-----------+---------------+-----------------+
| ID | STATE     | ERROR_MESSAGE | BINLOG_POSITION |
+----+-----------+---------------+-----------------+
|  1 | Completed |               |             742 |
+----+-----------+---------------+-----------------+
1 row in set (0.0004 sec)
```

## MySQL InnoDB Cluster

> [User Privileges](https://dev.mysql.com/doc/refman/8.0/en/mysql-innodb-cluster-production-deployment.html#mysql-innodb-cluster-user-privileges) : The user account used to administer an instance does not have to be the **root** account.
> The `dba.configureInstance()` method verifies that a suitable user is available for cluster usage, which is used for connections between members of the cluster. The **recommended way** to add a suitable user is to use the `clusterAdmin` and `clusterAdminPassword` options, which enable you to configure the cluster user and password when calling the function.


* Switching to JavaScript mode : `\js`
* Config local MySQL instance via TCP port 3306: `dba.configureInstance('root@localhost:3306', {clusterAdmin: "'cluster_admin'@'mysqlv8-1%'", clusterAdminPassword: 'cluster_admin_password'})`


### Configuration instance
**mysqlv8-1a:**
```
JS > dba.configureInstance('root@localhost:3306', {clusterAdmin: "'cluster_admin'@'mysqlv8-1%'", clusterAdminPassword: 'cluster_admin_password'});
Please provide the password for 'root@localhost:3306': ********
Save password for 'root@localhost:3306'? [Y]es/[N]o/Ne[v]er (default No): v
Configuring local MySQL instance listening at port 3306 for use in an InnoDB cluster...

This instance reports its own address as mysqlv8-1a:3306
Clients and other cluster members will communicate with it through this address by default. If this is not correct, the report_host MySQL system variable should be changed.

NOTE: Some configuration options need to be fixed:
+-----------------+---------------+----------------+----------------------------+
| Variable        | Current Value | Required Value | Note                       |
+-----------------+---------------+----------------+----------------------------+
| binlog_checksum | CRC32         | NONE           | Update the server variable |
+-----------------+---------------+----------------+----------------------------+

Do you want to perform the required configuration changes? [y/n]: y

Cluster admin user 'cluster_admin'@'mysqlv8-1%' created.
Configuring instance...
The instance 'localhost:3306' was configured for InnoDB cluster usage.
JS >
```
Perform `dba.configureInstance()` on **mysqlv8-1b** and **mysqlv8-1c**

### Create Cluster
**mysqlv8-1a:**
```
JS > \c cluster_admin@mysqlv8-1a
Creating a session to 'cluster_admin@mysqlv8-1a'
Please provide the password for 'cluster_admin@mysqlv8-1a': **********************
Save password for 'cluster_admin@mysqlv8-1a'? [Y]es/[N]o/Ne[v]er (default No): v
Fetching schema names for autocompletion... Press ^C to stop.
Closing old connection...
Your MySQL connection id is 15 (X protocol)
Server version: 8.0.18 MySQL Community Server - GPL
No default schema selected; type \use <schema> to set one.
JS >
```

Create the cluster: `dba.createCluster('cluster01')`:

```
JS > cluster=dba.createCluster('cluster01')
A new InnoDB cluster will be created on instance 'mysqlv8-1a:3306'.

Validating instance at mysqlv8-1a:3306...

This instance reports its own address as mysqlv8-1a:3306

Instance configuration is suitable.
Creating InnoDB cluster 'cluster01' on 'mysqlv8-1a:3306'...

Adding Seed Instance...
Cluster successfully created. Use Cluster.addInstance() to add MySQL instances.
At least 3 instances are needed for the cluster to be able to withstand up to
one server failure.

<Cluster:cluster01>
JS >
```
Show cluster status:
```
JS > cluster.status()
{
    "clusterName": "cluster01",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "mysqlv8-1a:3306",
        "ssl": "REQUIRED",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures.",
        "topology": {
            "mysqlv8-1a:3306": {
                "address": "mysqlv8-1a:3306",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": null,
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.18"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "mysqlv8-1a:3306"
}
JS >
```

### Add instance to the cluster:
**mysqlv8-1a:**
```
JS > cluster.addInstance('cluster_admin@mysqlv8-1b')
Please provide the password for 'cluster_admin@mysqlv8-1b': **********************
NOTE: A GTID set check of the MySQL instance at 'mysqlv8-1b:3306' determined that it
is missing transactions that were purged from all cluster members.
...
```

Select a recovery method
```
Please select a recovery method [C]lone/[A]bort (default Abort): C
Validating instance at mysqlv8-1b:3306...

This instance reports its own address as mysqlv8-1b:3306

Instance configuration is suitable.
A new instance will be added to the InnoDB cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Adding instance to the cluster...
...
```

Waiting for clone to finish:
```
Clone based state recovery is now in progress.

NOTE: A server restart is expected to happen as part of the clone process. If the
server does not support the RESTART command or does not come back after a
while, you may need to manually start it back.

* Waiting for clone to finish...
NOTE: mysqlv8-1b:3306 is being cloned from mysqlv8-1a:3306
** Stage DROP DATA: Completed
...
Stage RECOVERY: /####################################  100%  Completed** Stage RECOVERY: \
NOTE: mysqlv8-1b:3306 is shutting down...

* Waiting for server restart... ready
* mysqlv8-1b:3306 has restarted, waiting for clone to finish...
* Clone process has finished: 11.02 GB transferred in 15 min 30 sec (11.85 MB/s)

Incremental distributed state recovery is now in progress.

* Waiting for distributed recovery to finish...
NOTE: 'mysqlv8-1b:3306' is being recovered from 'mysqlv8-1a:3306'
* Distributed recovery has finished

The instance 'mysqlv8-1b' was successfully added to the cluster.
```
Show cluster status:
```
JS > cluster.status()
{
    "clusterName": "cluster01",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "mysqlv8-1a:3306",
        "ssl": "REQUIRED",
        "status": "OK_NO_TOLERANCE",
        "statusText": "Cluster is NOT tolerant to any failures.",
        "topology": {
            "mysqlv8-1a:3306": {
                "address": "mysqlv8-1a:3306",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": null,
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.18"
            },
            "mysqlv8-1b:3306": {
                "address": "mysqlv8-1b:3306",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": null,
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.18"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "mysqlv8-1a:3306"
}
JS >
```

Add instance `mysqlv8-1c` to cluster. Waiting for clone to finish:
```
JS > cluster.addInstance('cluster_admin@mysqlv8-1c')
...
The instance 'mysqlv8-1c' was successfully added to the cluster
JS >
```

Show cluster status:
```
JS > cluster.status()
{
    "clusterName": "cluster01",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "mysqlv8-1a:3306",
...
            "mysqlv8-1b:3306": {
                "address": "mysqlv8-1b:3306",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": null,
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.18"
            },
            "mysqlv8-1c:3306": {
                "address": "mysqlv8-1c:3306",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": null,
                "role": "HA",
                "status": "ONLINE",
                "version": "8.0.18"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "mysqlv8-1a:3306"
}
JS >
```

### Fixed
* `Cluster.addInstance`: Cannot add an instance with the **same server UUID**. Fixed by removing that auto generated `/var/lib/mysql/auto.conf` file and restart MySQL server. After that you will get a new `/var/lib/mysql/auto.conf` file with a new server UUID.

* Get a temporay password from `mysqld.log` after fresh installation mysql
```
grep passwd /var/log/mysqld.log
```
