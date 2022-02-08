---
layout: post
title: "ProxySQL: Configure Monitoring"
categories: [mysql]
---


### Creating & Monitoring User 

```sql
-- create `proxysql` user
CREATE USER 'proxysql'@'%' IDENTIFIED WITH mysql_native_password by  'pmm_mon1t0r';

-- grant privilelge
GRANT USAGE ON *.* TO 'proxysql'@'%';
```

### [Update `mysql-monitor_username`][1]
```sql
UPDATE global_variables
SET variable_value='proxysql'
WHERE variable_name='mysql-monitor_username';
```

### Update `mysql-monitor_password`
```sql
UPDATE global_variables
SET variable_value='pmm_mon1t0r'
WHERE variable_name='mysql-monitor_password';
```

### Update monitoring intervals
```sql
UPDATE global_variables
SET variable_value='2000'
WHERE variable_name
IN (
  'mysql-monitor_connect_interval',
  'mysql-monitor_ping_interval',
  'mysql-monitor_read_only_interval');
```

### Display monitor variables
```sql
SELECT * FROM global_variables
WHERE variable_name
LIKE 'mysql-monitor_%';
```
**result:**
```sql
+--------------------------------------------------------------+------------------+
| variable_name                                                | variable_value   |
+--------------------------------------------------------------+------------------+
| mysql-monitor_enabled                                        | true             |
| mysql-monitor_connect_timeout                                | 600              |
| mysql-monitor_ping_max_failures                              | 3                |
| mysql-monitor_ping_timeout                                   | 1000             |
| mysql-monitor_read_only_max_timeout_count                    | 3                |
| mysql-monitor_replication_lag_interval                       | 10000            |
| mysql-monitor_replication_lag_timeout                        | 1000             |
| mysql-monitor_groupreplication_healthcheck_interval          | 5000             |
| mysql-monitor_groupreplication_healthcheck_timeout           | 800              |
| mysql-monitor_groupreplication_healthcheck_max_timeout_count | 3                |
| mysql-monitor_groupreplication_max_transactions_behind_count | 3                |
| mysql-monitor_galera_healthcheck_interval                    | 5000             |
| mysql-monitor_galera_healthcheck_timeout                     | 800              |
| mysql-monitor_galera_healthcheck_max_timeout_count           | 3                |
| mysql-monitor_replication_lag_use_percona_heartbeat          |                  |
| mysql-monitor_query_interval                                 | 60000            |
| mysql-monitor_query_timeout                                  | 100              |
| mysql-monitor_slave_lag_when_null                            | 60               |
| mysql-monitor_threads_min                                    | 8                |
| mysql-monitor_threads_max                                    | 128              |
| mysql-monitor_threads_queue_maxsize                          | 128              |
| mysql-monitor_wait_timeout                                   | true             |
| mysql-monitor_writer_is_also_reader                          | true             |
| mysql-monitor_username                                       | proxysql_monitor |
| mysql-monitor_password                                       | sql_mon1t0r      |
| mysql-monitor_history                                        | 600000           |
| mysql-monitor_connect_interval                               | 2000             |
| mysql-monitor_ping_interval                                  | 2000             |
| mysql-monitor_read_only_interval                             | 2000             |
| mysql-monitor_read_only_timeout                              | 500              |
+--------------------------------------------------------------+------------------+
```

### Load variables to runtime
```sql
LOAD MYSQL VARIABLES TO RUNTIME;
```

### Permanently stored variables to disk
```sql
SAVE MYSQL VARIABLES TO DISK;
```

## Backend’s health check

### Show tables from `monitor` database
```sql
proxy_admin> show tables from monitor;
+--------------------------------------+
| tables                               |
+--------------------------------------+
| mysql_server_aws_aurora_check_status |
| mysql_server_aws_aurora_failovers    |
| mysql_server_aws_aurora_log          |
| mysql_server_connect_log             |
| mysql_server_galera_log              |
| mysql_server_group_replication_log   |
| mysql_server_ping_log                |
| mysql_server_read_only_log           |
| mysql_server_replication_lag_log     |
+--------------------------------------+
```

### Display `mysql_server_connect_log`
```sql
SELECT *
FROM monitor.mysql_server_connect_log
ORDER BY time_start_us DESC
LIMIT 10;
```

**result:**
```sql
+--------------+------+------------------+-------------------------+---------------+
| hostname     | port | time_start_us    | connect_success_time_us | connect_error |
+--------------+------+------------------+-------------------------+---------------+
| 10.10.10.22  | 3306 | 1595089624174123 | 1247                    | NULL          |
| 10.10.10.210 | 3306 | 1595089624140255 | 785                     | NULL          |
| 10.10.10.210 | 3306 | 1595089622176072 | 792                     | NULL          |
| 10.10.10.22  | 3306 | 1595089622140146 | 1254                    | NULL          |
| 10.10.10.210 | 3306 | 1595089620167651 | 786                     | NULL          |
| 10.10.10.22  | 3306 | 1595089620140042 | 1314                    | NULL          |
| 10.10.10.210 | 3306 | 1595089618164266 | 782                     | NULL          |
| 10.10.10.22  | 3306 | 1595089618139939 | 1348                    | NULL          |
| 10.10.10.22  | 3306 | 1595089616176055 | 1341                    | NULL          |
| 10.10.10.210 | 3306 | 1595089616139823 | 779                     | NULL          |
+--------------+------+------------------+-------------------------+---------------+

```

## Display `mysql_server_ping_log`
```sql
SELECT *
FROM monitor.mysql_server_ping_log
ORDER BY time_start_us DESC
LIMIT 10;
```
**result:**
```sql
+--------------+------+------------------+----------------------+------------+
| hostname     | port | time_start_us    | ping_success_time_us | ping_error |
+--------------+------+------------------+----------------------+------------+
| 10.10.10.210 | 3306 | 1595089666423547 | 160                  | NULL       |
| 10.10.10.22  | 3306 | 1595089666396191 | 398                  | NULL       |
| 10.10.10.210 | 3306 | 1595089664420034 | 157                  | NULL       |
| 10.10.10.22  | 3306 | 1595089664396076 | 406                  | NULL       |
| 10.10.10.22  | 3306 | 1595089662416829 | 394                  | NULL       |
| 10.10.10.210 | 3306 | 1595089662396027 | 160                  | NULL       |
| 10.10.10.22  | 3306 | 1595089660430673 | 407                  | NULL       |
| 10.10.10.210 | 3306 | 1595089660395922 | 163                  | NULL       |
| 10.10.10.210 | 3306 | 1595089658418224 | 166                  | NULL       |
| 10.10.10.22  | 3306 | 1595089658395839 | 398                  | NULL       |
+--------------+------+------------------+----------------------+------------+

```
> One important thing to note here is that monitoring on connect and ping is performed based on the content of the table `mysql_servers`, **even before this is loaded to RUNTIME**. This approach is intentional: in this way it is possible to **perform basic health checks before adding the nodes in production**. [1][1]

### Load servers to runtime

```sql
-- load servers to runtime
LOAD MYSQL SERVERS TO RUNTIME;

-- display `mysql_servers`
SELECT hostgroup_id, hostname, port, status
FROM mysql_servers;
```
**result:**
```sql
+--------------+--------------+------+--------+
| hostgroup_id | hostname     | port | status |
+--------------+--------------+------+--------+
| 1            | 10.10.10.22  | 3306 | ONLINE |
| 1            | 10.10.10.210 | 3306 | ONLINE |
+--------------+--------------+------+--------+
```

[1]: https://proxysql.com/documentation/ProxySQL-Configuration/ "configure ProxySQL"
