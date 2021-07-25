---
layout : post
title : "Replicating from MySQL 5.7 Master to MariaDB 10.2 Slave"
categories : [mysql, mariadb]
published : true
---

## Master
```
Host : Ubuntu 16.04
IP Address : 10.10.10.22
Database : Percona XtraDB Cluster 5.7
Backup Tool : Percona xtrabackup version 2.4
```

### Backup and prepare

```shell
# Backup
$ sudo xtrabackup --backup \
  --user=xtrabackup_usr \
  --password=xtrabackup_passwd \
  --target-dir=/opt/perconaXtraBackup/xtrabackup_dir/
    ...
    innobackupex: completed OK!

# Prepare
$ sudo xtrabackup --user=xtrabackup_usr \
  --password=xtrabackup_passwd \
  --apply-log /opt/perconaXtraBackup/xtrabackup_dir/
    ...
    InnoDB: Shutdown completed; log sequence number 39984943813
    160128 12:41:38 completed OK
```
### Rsync data to slave

slave:
```
# Create backup dir
$ sudo mkdir /opt/perconaXtraBackup
$ sudo chown -R ubuntu:ubuntu /opt/perconaXtraBackup
```

master:
```
# rsync to slave
$ sudo rsync -avpP -e ssh /opt/perconaXtraBackup/xtrabackup_dir/ \
ubuntu@10.10.10.210:/opt/perconaXtraBackup/
```

### Replication user & Grant access

```sql
mysql> CREATE USER 'repl'@'10.10.10.210' IDENTIFIED BY 'repl_password';
mysql> GRANT REPLICATION SLAVE ON *.* TO 'repl'@'10.10.10.210';
mysql> FLUSH PRIVILEGES;
```


## Slave
```
Host : Ubuntu 18.04
IP Address : 10.10.10.210
Database : MariaDB 10.2
```
[In MariaDB 10.2, Percona XtraBackup 2.4 is supported][2] in some cases if **InnoDB page compression** is not used, and if **data at rest encryption** is not used, and if **innodb_page_size** is set to 16k.

### [Install MariaDB 10.2 on Ubuntu 18.04 LTS][1]

Install via APT (Debian/Ubuntu)
1. Configure the APT package repository.

```shell
$ wget https://downloads.mariadb.com/MariaDB/mariadb_repo_setup

$ echo "32e01fbe65b4cecc074e19f04c719d1a600e314236c3bb40d91e555b7a2abbfc mariadb_repo_setup" \
    | sha256sum -c -

    mariadb_repo_setup: OK

$ chmod +x mariadb_repo_setup

$ sudo ./mariadb_repo_setup \
   --mariadb-server-version="mariadb-10.2"

   [info] Checking for script prerequisites.
   [info] Repository file successfully written to /etc/apt/sources.list.d/mariadb.list
   [info] Adding trusted package signing keys...
   [info] Running apt-get update...
   [info] Done adding trusted package signing keys

```

2. Install MariaDB Community Server and package dependencies:

```shell
$ sudo apt install mariadb-server mariadb-backup-10.2

  Reading package lists... Done
  Building dependency tree
  ...
  After this operation, 205 MB of additional disk space will be used.
  Do you want to continue? [Y/n] Y

```

### Import the data

```shell
# Stop service
$ sudo service mysql stop

# Back up the original
$ sudo mkdir /tmp/mysql
$ sudo mv /var/lib/mysql/* /tmp/mysql/

# Move snapshot
$ sudo mv /opt/perconaXtraBackup/* /var/lib/mysql/

# Change permissions
$ sudo  chown mysql:mysql /var/lib/mysql

```

### Configure `my.cnf`.

```
[mysqld]
# The server id is a unique number for each MariaDB/MySQL server in your network.
server-id               = 10218
```

### Setup Slave

```shell
# Start service
$ sudo service mysql start

# Finding the binary log position
$ sudo cat /var/lib/mysql/xtrabackup_binlog_info
percona1022-bin.000202	989027851	28a606b1-2ce7-11e8-98cd-1c98ec1359d0:1-2,
59ef0c21-bbbf-ee18-7c28-c5c7f4aa2346:1-278889045
```

**master_log_file** : percona1022-bin.000202

**master_log_pos** : 989027851

```sql
mysql> STOP SLAVE;

mysql> CHANGE MASTER TO
    MASTER_HOST ='10.10.10.21',
    MASTER_USER ='repl',
    MASTER_PASSWORD ='repl_password',
    MASTER_LOG_FILE = 'percona1022-bin.000202',
    MASTER_LOG_POS = 989027851;

mysql> START SLAVE;

mysql> SHOW SLAVE STATUS \G
         ...
         Slave_IO_Running: Yes
         Slave_SQL_Running: Yes
         ...
```

[1]: https://mariadb.com/docs/deploy/upgrade-community-server-cs102-ubuntu18/#uninstall-the-old-version "MariaDB Community Server 10.2 on Ubuntu"

[2]: https://mariadb.com/kb/en/percona-xtrabackup-overview/#compatibility-with-mariadb-102 "compatibility-with-mariadb-102"
