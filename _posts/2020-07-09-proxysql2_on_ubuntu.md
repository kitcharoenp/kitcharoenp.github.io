---
layout: post
title: "ProxySQL on Ubuntu"
categories: [mysql]
---
### [Installing][1]

* **Adding the repository:**
```shell
wget -O - 'https://repo.proxysql.com/ProxySQL/repo_pub_key' | apt-key add -
echo deb https://repo.proxysql.com/ProxySQL/proxysql-2.0.x/$(lsb_release -sc)/ ./ \
| tee /etc/apt/sources.list.d/proxysql.list
```
![ProxySQL respo ](/assets/img/blog/2020-07-09_01.png)

* **Installing ProxySQL:**
```shell
$ sudo apt update
$ sudo apt install proxysql
$ proxysql --version
ProxySQL version 2.0.12-38-g58a909a0, codename Truls
```

* **Service Status:**
```shell
$ sudo service proxysql status
● proxysql.service - High Performance Advanced Proxy for MySQL
   Loaded: loaded (/lib/systemd/system/proxysql.service; enabled; vendor preset: enabled)
   Active: inactive (dead)   
```
* **Service Start:**
```shell
$ sudo service proxysql start
$ sudo service proxysql status
● proxysql.service - High Performance Advanced Proxy for MySQL
   Loaded: loaded (/lib/systemd/system/proxysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-07-09 08:54:45 UTC; 2s ago
  Process: 1141 ExecStart=/usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf $PROXYSQL_O
 Main PID: 1143 (proxysql)
    Tasks: 24 (limit: 4915)
   CGroup: /system.slice/proxysql.service
           ├─1143 /usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf
           └─1144 /usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf
```

### Configuring
> First of all, bear in mind that the best way to configure ProxySQL is through its admin interface [2][2]

* **Install mysql-client:**
  ```shell
  $ sudo apt install mysql-client
  ```   

* **Changing the mysql prompt:** [4][4]

  Open your MySQL **my.cnf**, add the following line in the `[mysql]` section. If the file does not have one, create it by adding the following line.

  **my.cnf:**
  ```
  [mysql]

  prompt=\u@\h:[\d]>\_
  ```

* **Connect proxysql:**
  ```shell
  $ mysql -u admin -padmin -h 127.0.0.1 -P6032
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 4
  Server version: 5.5.30 (ProxySQL Admin Module)
  ...
  admin@127.0.0.1:[(none)]>
  ```

### Add backends

```sql
admin@127.0.0.1:[main]> INSERT INTO mysql_servers(hostgroup_id,hostname,port) VALUES (1,'10.10.10.22',3306);
...
admin@127.0.0.1:[main]> SELECT hostgroup_id, hostname, port FROM mysql_servers;
+--------------+-------------+------+
| hostgroup_id | hostname    | port |
+--------------+-------------+------+
| 1            | 10.10.10.22 | 3306 |
+--------------+-------------+------+
```

### [Configure monitoring][3]
**set `mysql-monitor_username`**
```sql
admin@127.0.0.1:[main]> UPDATE global_variables SET variable_value='proxysql_monitor' WHERE variable_name='mysql-monitor_username';
```

**set `mysql-monitor_password`**
```sql
admin@127.0.0.1:[main]> UPDATE global_variables SET variable_value='sql_mon1t0r' WHERE variable_name='mysql-monitor_password';
```

**set monitoring intervals**
```sql
admin@127.0.0.1:[main]> UPDATE global_variables SET variable_value='2000' WHERE variable_name IN ('mysql-monitor_connect_interval','mysql-monitor_ping_interval','mysql-monitor_read_only_interval');


admin@127.0.0.1:[main]> SELECT * FROM global_variables WHERE variable_name LIKE 'mysql-monitor_%';
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

**load variables to runtime**
```sql
admin@127.0.0.1:[main]> LOAD MYSQL VARIABLES TO RUNTIME;
```

**permanently stored variables to disk**
```sql
admin@127.0.0.1:[main]> SAVE MYSQL VARIABLES TO DISK;
```

### Backend’s health check

**show tables from `monitor` database**
```sql
admin@127.0.0.1:[(none)]> show tables from monitor;
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


**show `mysql_server_connect_log`**
```sql
admin@127.0.0.1:[main]> SELECT * FROM monitor.mysql_server_connect_log ORDER BY time_start_us DESC LIMIT 10;
+-------------+------+------------------+-------------------------+---------------+
| hostname    | port | time_start_us    | connect_success_time_us | connect_error |
+-------------+------+------------------+-------------------------+---------------+
| 10.10.10.22 | 3306 | 1595075035388967 | 1295                    | NULL          |
| 10.10.10.22 | 3306 | 1595075033383304 | 1321                    | NULL          |
| 10.10.10.22 | 3306 | 1595075031387324 | 1272                    | NULL          |
| 10.10.10.22 | 3306 | 1595075029388231 | 1303                    | NULL          |
| 10.10.10.22 | 3306 | 1595075027379849 | 1321                    | NULL          |
| 10.10.10.22 | 3306 | 1595075025378072 | 1342                    | NULL          |
| 10.10.10.22 | 3306 | 1595075023382534 | 1313                    | NULL          |
| 10.10.10.22 | 3306 | 1595075021374117 | 1253                    | NULL          |
| 10.10.10.22 | 3306 | 1595075019361839 | 1344                    | NULL          |
| 10.10.10.22 | 3306 | 1595075017382115 | 1340                    | NULL          |
+-------------+------+------------------+-------------------------+---------------+
```
**show `mysql_server_ping_log`**
```sql
SELECT * FROM monitor.mysql_server_ping_log ORDER BY time_start_us DESC LIMIT 10;
+-------------+------+------------------+----------------------+------------+
| hostname    | port | time_start_us    | ping_success_time_us | ping_error |
+-------------+------+------------------+----------------------+------------+
| 10.10.10.22 | 3306 | 1595077469784110 | 409                  | NULL       |
| 10.10.10.22 | 3306 | 1595077467779426 | 409                  | NULL       |
| 10.10.10.22 | 3306 | 1595077465780658 | 405                  | NULL       |
| 10.10.10.22 | 3306 | 1595077463785096 | 407                  | NULL       |
| 10.10.10.22 | 3306 | 1595077461793182 | 406                  | NULL       |
| 10.10.10.22 | 3306 | 1595077459789732 | 407                  | NULL       |
| 10.10.10.22 | 3306 | 1595077457774877 | 405                  | NULL       |
| 10.10.10.22 | 3306 | 1595077455798893 | 404                  | NULL       |
| 10.10.10.22 | 3306 | 1595077453779528 | 407                  | NULL       |
| 10.10.10.22 | 3306 | 1595077451774810 | 398                  | NULL       |
+-------------+------+------------------+----------------------+------------+

```
> One important thing to note here is that monitoring on connect and ping is performed based on the content of the table `mysql_servers`, **even before this is loaded to RUNTIME**. This approach is intentional: in this way it is possible to **perform basic health checks before adding the nodes in production**. [3][3]

* **enable servers**
```
LOAD MYSQL SERVERS TO RUNTIME;
```


[1]: https://proxysql.com/documentation/installing-proxysql/ "Installing ProxySQL"

[2]: https://proxysql.com/documentation/getting-started/ "Getting started"

[3]: https://proxysql.com/documentation/ProxySQL-Configuration/ "configure ProxySQL"

[4]: https://www.codediesel.com/mysql/changing-mysql-clients-default-prompt/ "Changing MySQL Prompt"
