---
layout : post
title : "Cron script backup mariaDB"
categories : [mariadb]
published : true
---
### Testing `mariabackup`
```shell
# test connection
$ mysql -u xtrabackup_usr -p

# mariabackup
$ sudo mariabackup --backup \
    --target-dir=/opt/mariabackup/2021-07-25/ \
    --user="xtrabackup_usr" \
    --password="s3cret" \
    --no-timestamp \
    --parallel=4

    ...
    [00] 2021-07-25 11:19:57 Writing xtrabackup_info
    [00] 2021-07-25 11:19:57         ...done
    [00] 2021-07-25 11:19:57 Redo log (from LSN 434414921436 to 434414921445) was copied.
    [00] 2021-07-25 11:19:57 completed OK!

# List files
$ ls /opt/mariabackup/2021-07-25/
aria_log.00000001  ib_buffer_pool  mysql               report_partner          xtrabackup_checkpoints  zurmo
aria_log_control   ib_logfile0     performance_schema  sys                     xtrabackup_galera_info
backup-my.cnf      ibdata1         phpmyadmin          xtrabackup_binlog_info  xtrabackup_inf

# View info
$ sudo cat xtrabackup_info
  uuid = 3e452bfe-ed3a-11eb-bfec-00163e902876
  name =
  tool_name = mariabackup
  tool_command = --backup --target-dir=/opt/mariabackup/2021-07-25/ --user=xtrabackup_usr --password=... --no-timestamp --parallel=4
  tool_version = 10.2.39-MariaDB
  ibbackup_version = 10.2.39-MariaDB
  server_version = 10.2.39-MariaDB-1:10.2.39+maria~bionic-log
  start_time = 2021-07-25 11:17:34
  end_time = 2021-07-25 11:19:57
  lock_time = 0
  binlog_pos = filename 'mariadb-bin.000007', position '344', GTID of the last change '0-10218-433'
  innodb_from_lsn = 0
  innodb_to_lsn = 434414921436
  partial = N
  incremental = N
  format = file
  compressed = N


# prepare
$ sudo mariabackup --prepare \
    --target-dir=/opt/mariabackup/

    ...
    [00] 2021-07-25 11:22:23 mariabackup: Recovered WSREP position: a610f3de-4440-11e7-83d7-3a380b55dcb9:282082904
    [00] 2021-07-25 11:22:24 completed OK!
```

### Create `cron.daily` Script
Create `cron` script named `xtrabackup_full_backup` on `/etc/cron.daily/`

```shell
$ sudo nano /etc/cron.daily/xtrabackup_full_backup
```

**copy & past:**
```shell
#!/bin/bash
# Get year-month-day from current date - 3 days
DATE_MINUS_3=`date +%Y-%m-%d -d "3 day ago"`

# Get year-month-day from current date
DATE=`date +%Y-%m-%d`

# Set `DST` datadir
DIR=/opt/mariabackup/
DST=$DIR/${DATE}

# Set `DEL_DIR` datadir
DEL_DIR=$DIR/${DATE_MINUS_3}

# redirect stdout/stderr to a file
exec &> $DIR/cron_log_$DATE.log

# create `DST` datadir
mkdir -p $DST

# clear content in  `DST` datadir
rm -rf $DST/*

# run mariabackup
mariabackup --backup \
    --target-dir=$DST \
    --user="xtrabackup_usr" \
    --password="s3cret" \
    --no-timestamp \
    --parallel=4 \
#    --databases="databasename1[.table_name1] databasename2[.table_name2] ..." \
#    --rsync \
#    --host \
#    --port \
#    --socket \

# prepare
mariabackup --prepare \
    --target-dir=$DST \

# delete folder which name is current date - 3 days
rm -Rf $DEL_DIR

# delete files older than 30 days
#find $DIR -type f -mtime +30 -delete
```

**Add Execute Privilege:**
```shell
$ sudo chmod +x /etc/cron.daily/xtrabackup_full_backup
```

### [--parallel][1]
Defines the number of threads to use for parallel data file transfer.

[1]: https://mariadb.com/kb/en/mariabackup-options/#-parallel "Defines the number of threads"
