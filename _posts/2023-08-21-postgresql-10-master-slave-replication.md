---
layout : post
title : "PostgreSQL 10 Master-Slave Replication"
categories : [postgresql]
published : false
---

## Master : Configuring


### Create a user `replica`


Establish a database user called `replica` with password `replica@S3cr3t3` who will perform the replication task.

```
CREATE USER replica REPLICATION LOGIN CONNECTION LIMIT 1 ENCRYPTED PASSWORD 'replica@S3cr3t3';
```

```postgresql
postgres=# CREATE USER replica REPLICATION LOGIN CONNECTION LIMIT 1 ENCRYPTED PASSWORD 'replica@S3cr3t3';
CREATE ROLE
postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 replica   | Replication                                               +| {}
           | 1 connection                                               | 
 ubuntu    | Superuser                                                  | {}
```

Change the maximum number of connections allowed to the `replica` user.

**`-1` means no limit**
```postgresql
postgres=# ALTER ROLE replica CONNECTION LIMIT -1;
ALTER ROLE
postgres=# \du
                                   List of roles
 Role name |                         Attributes                         | Member of 
-----------+------------------------------------------------------------+-----------
 postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 replica   | Replication                                                | {}
 ubuntu    | Superuser                                                  | {}
```

### Update PostgreSQL's main configuration file
`/etc/postgresql/10/main/postgresql.conf`

```console
listen_addresses = 'localhost,master_ip_address'

wal_level = replica                     # minimal, replica, or logical
                                        # (change requires restart)
#------------------------------------------------------------------------------
# REPLICATION
#------------------------------------------------------------------------------

# - Sending Server(s) -

# Set these on the master and on any standby that will send replication data.

max_wal_senders = 10            # max number of walsender processes
                                # (change requires restart)
wal_keep_segments = 64          # in logfile segments, 16MB each; 0 disables
```
Replace the `master_ip_address` with your ip addrsss of **Master** postgresql server


### Update PostgreSQL's access policy configuration file
`/etc/postgresql/10/main/pg_hba.conf`


```
# TYPE  DATABASE        USER        ADDRESS                       METHOD
host    replication     replica     replication_psql_ip_address/0 md5
```


## Slave : Configuring


### Update PostgreSQL's main configuration file
`/etc/postgresql/10/main/postgresql.conf`

```console
listen_addresses = 'localhost,slave_ip_address'

wal_level = replica
max_wal_senders = 10
wal_keep_segments = 64

hot_standby = on                        # "off" disallows queries during recovery
                                        # (change requires restart)
```


### Update PostgreSQL's access policy configuration file
`/etc/postgresql/10/main/pg_hba.conf`


```
# TYPE  DATABASE        USER        ADDRESS                    METHOD
host    replication     replica     master_ip_address/0   md5
```


### Delete all PostgreSQL data directory files and directories

```bash
cd /var/lib/postgresql/10/main

rm -rfv *
```

### Copy all data from the master database

```bash
sudo su postgres
pg_basebackup -h master_ip_address -U replica -p 5432 -D /var/lib/postgresql/10/main/  -Fp -Xs -P -R
```

It will prompt for a password for the **replica** PostgreSQL user, which is in our case `replica@S3cr3t3`.

### Reference
* [PostgreSQL Master-Slave Database Replication](https://blog.devgenius.io/postgresql-master-slave-database-replication-a845777901ab)
