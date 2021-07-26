---
layout : post
title : "MariaDB: Replicating MariaDB 10.2 to MariaDB 10.5"
categories : [mariadb, replication]
published : true
---
The terms master and slave have historically been used in replication, but the terms terms primary and replica are now preferred. MariaDB 10.5 has begun the process of renaming. [1][1]


Replication with **global transaction IDs**. These have a number of benefits, and it is generally recommended to use this feature from MariaDB 10.0.

### Versions
In general, when replicating across different versions of MariaDB, it is best that the **master is an older version than the slave**. MariaDB versions are usually backward compatible.

**primary:**
```
Host : Ubuntu 18.04
IP Address : 10.10.10.210
Database : MariaDB 10.2
Backup Tool : Mariabackup
Backup Dir : /opt/mariabackup/
```

**replica:**
```
Host : Ubuntu 20.04
IP Address : 10.10.10.211
Database : MariaDB 10.5
```

# Primary

### Configuring
1. Give the master a unique **server_id**

2. Enable binary logging  if it's not already enabled.

  **/etc/mysql/my.cnf:**
  ```
  server-id               = 10210
  log_bin                 = /var/log/mysql/mariadb-bin
  ```

3. Slaves will need permission to connect and start replicating from a server.

  ```sql
  CREATE USER 'repl'@'10.10.10.211' IDENTIFIED BY 'repl_password';
  GRANT REPLICATION SLAVE ON *.* TO 'repl'@'10.10.10.211';
  FLUSH PRIVILEGES;
  ```

### [Backup and prepare][3]

  ```shell
  # mariabackup
  # source database server is the desired replication master
  $ sudo mariabackup --backup \
    --target-dir=/opt/mariabackup/for_replica/ \
    --user="mariabackup_usr" \
    --password="mariabackup_passwd" \
    --no-timestamp \
    --parallel=4
      ...
      completed OK!

  # if source database server is a replication slave
  $ sudo mariabackup --backup \
    --slave-info --safe-slave-backup \
    --target-dir=/opt/mariabackup/for_replica/ \
    --user="mariabackup_usr" \
    --password="mariabackup_passwd" \
    --no-timestamp \
    --parallel=4
      ...
      completed OK!

  # Prepare
  $ sudo mariabackup --prepare \
    --target-dir=/opt/mariabackup/for_replica/
      ...
      [00] 2021-07-26 06:14:10 mariabackup: Recovered WSREP position: a610f3de-4440-11e7-83d7-3a380b55dcb9:282082904
      [00] 2021-07-26 06:14:11 completed OK!

  # Get the replication coordinates of the master
  $ sudo cat /opt/mariabackup/for_replica/xtrabackup_binlog_info
  mariadb-bin.000006	330

  # change permission
  $ sudo chown -R ubuntu:ubuntu /opt/mariabackup/for_replica/
  ```


# Replica


### [Install MariaDB 10.5 on Ubuntu 20.04 LTS][2]

1. Configure the APT package repository.

```shell
$ wget https://downloads.mariadb.com/MariaDB/mariadb_repo_setup

$ echo "32e01fbe65b4cecc074e19f04c719d1a600e314236c3bb40d91e555b7a2abbfc mariadb_repo_setup" \
    | sha256sum -c -

    mariadb_repo_setup: OK

$ chmod +x mariadb_repo_setup

$ sudo ./mariadb_repo_setup \
   --mariadb-server-version="mariadb-10.5"

   [info] Checking for script prerequisites.
   [info] Repository file successfully written to /etc/apt/sources.list.d/mariadb.list
   [info] Adding trusted package signing keys...
   [info] Running apt-get update...
   [info] Done adding trusted package signing keys

```

2. Install MariaDB

```shell
$ sudo apt install mariadb-server mariadb-backup

  Reading package lists... Done
  Building dependency tree
  ...
  After this operation, 256 MB of additional disk space will be used.
  Do you want to continue? [Y/n] Y
```

3. Check service

```shell
$ sudo service mariadb status
● mariadb.service - MariaDB 10.5.11 database server
     Loaded: loaded (/lib/systemd/system/mariadb.service; enabled; vendor preset: enabled)
    Drop-In: /etc/systemd/system/mariadb.service.d
             └─migrated-from-my.cnf-settings.conf
     Active: active (running) since Mon 2021-07-26 03:03:05 UTC; 55s ago
       Docs: man:mariadbd(8)
             https://mariadb.com/kb/en/library/systemd/
...
Jul 26 03:03:23 p105 /etc/mysql/debian-start[2230]: Phase 7/7: Running 'FLUSH PRIVILEGES'
Jul 26 03:03:23 p105 /etc/mysql/debian-start[2230]: OK

```

### Configuring the Replica
**my.cnf:**
  ```
  [mariadb]
  log-bin
  server_id=10211
  read_only
  ```
  `binlog-format` specifies how your statements are logged. This mainly affects the size of the binary log that is sent between the Master and the Slaves.

  The default, since MariaDB 10.2.3, is **mixed** logging which is **replication-safe and requires less storage space than** row logging.


### Copy the Backup to the New Replica
copy backup from primary the new replica
```shell
$ sudo rsync -avP  -e ssh ubuntu@10.10.10.210:/opt/mariabackup/for_replica/ /opt/mariabackup/
```

### [Restore the Backup on the New Replica][3]
restore the backup to the datadir

```shell
# Stop service
$ sudo service mariadb stop

# Back up the original
$ sudo mkdir /tmp/mysql
$ sudo mv /var/lib/mysql/* /tmp/mysql

# Move backup
$ sudo mariabackup --copy-back \
   --target-dir=/opt/mariabackup/

# Change permissions
$ sudo chown -R mysql:mysql /var/lib/mysql/
```

### Configure the New Replica

```shell
# Start service
$ sudo service mariadb start

# Finding the binary log position
$ cat /opt/mariabackup/xtrabackup_binlog_info
mariadb-bin.000006	330

# test connection to primary
$ mariadb --host=10.10.10.210 --user=repl --password=repl_password

  Welcome to the MariaDB monitor.  Commands end with ; or \g.
  Your MariaDB connection id is 21
  Server version: 10.2.39-MariaDB-1:10.2.39+maria~bionic-log mariadb.org binary distribution
  ...
  MariaDB [(none)]>
```

`mariadb-upgrade` utility which can be used to identify and correct compatibility issues in the new version

```shell
$ sudo mariadb-upgrade -u root -p
 Phase 1/7: Checking and upgrading mysql database
 Processing databases
 mysql
 mysql.column_stats   
 ...
 Phase 7/7: Running 'FLUSH PRIVILEGES'
 OK
```


Start the Slave
```sql
STOP SLAVE;

CHANGE MASTER TO
    MASTER_HOST ='10.10.10.210',
    MASTER_USER ='repl',
    MASTER_PASSWORD ='repl_password',
    MASTER_LOG_FILE = 'mariadb-bin.000006',
    MASTER_LOG_POS = 330;

START SLAVE;
```

Check that the replication is working

```sql
SHOW SLAVE STATUS \G
         ...
         Slave_IO_Running: Yes
         Slave_SQL_Running: Yes
         ...
```


[1]: https://mariadb.com/kb/en/setting-up-replication/ "Setting Up Replication"

[2]: https://mariadb.com/docs/deploy/upgrade-community-server-cs105-ubuntu20/ "MariaDB 10.5 on Ubuntu 20.04"

[3]: https://mariadb.com/kb/en/setting-up-a-replication-slave-with-mariabackup/ "Setting up a Replication Slave with Mariabackup"
