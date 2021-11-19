---
layout : post
title : "MariaDB MaxScale"
categories : [mariadb,]
published : true
---
### [Installing MaxScale¶][1]

```shell
$ curl -LsS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
[info] Checking for script prerequisites.
[info] Repository file successfully written to /etc/apt/sources.list.d/mariadb.list
[info] Adding trusted package signing keys...
[info] Running apt-get update...
[info] Done adding trusted package signing keys

$ sudo apt install maxscale
$ maxscale --version
MaxScale 6.1.1
```


### Creating a user account for MaxScale
 Create a special user account for checks that incoming clients are valid. Executing the following SQL commands on the master server of your database cluster. 

 ```sql
CREATE USER 'maxscale'@'10.10.10.%' IDENTIFIED BY 'maxscale_s3cret';
GRANT SELECT ON mysql.user TO 'maxscale'@'10.10.10.%';
GRANT SELECT ON mysql.db TO 'maxscale'@'10.10.10.%';
GRANT SELECT ON mysql.tables_priv TO 'maxscale'@'10.10.10.%';
GRANT SELECT ON mysql.columns_priv TO 'maxscale'@'10.10.10.%';
GRANT SELECT ON mysql.proxies_priv TO 'maxscale'@'10.10.10.%';
GRANT SELECT ON mysql.roles_mapping TO 'maxscale'@'10.10.10.%';
GRANT SHOW DATABASES ON *.* TO 'maxscale'@'10.10.10.%';
FLUSH PRIVILEGES;
 ```


### Creating client user accounts

### Creating the configuration file

### Configuring the servers

### Configuring the monitor

```sql
CREATE USER 'maxscale_monitor'@'10.10.10.%' IDENTIFIED BY 'maxscale_mon_s3cret';
GRANT REPLICATION CLIENT on *.* to 'maxscale_monitor'@'10.10.10.%';
FLUSH PRIVILEGES;
```



### Configuring the services and listeners

The user and password parameters define the credentials the service uses to populate user authentication data.



**mysql.procs_priv**
> warning: Using old user account query due to insufficient privileges. To avoid this warning, give the service user of 'Read-Only-Service' access to the 'mysql.procs_priv'-table.

```
GRANT SELECT ON mysql.proxies_priv TO 'maxscale'@'maxscalehost';
```

### Starting MaxScale

```shell
# start service
sudo service maxscale start

# view service status
sudo journalctl -u maxscale.service -n200 -f 
```

### Checking MaxScale status with MaxCtrl

```shell
ubuntu@maxscale:~$ sudo maxctrl list servers
┌─────────┬──────────────┬──────┬─────────────┬─────────────────────────────┬──────┐
│ Server  │ Address      │ Port │ Connections │ State                       │ GTID │
├─────────┼──────────────┼──────┼─────────────┼─────────────────────────────┼──────┤
│ primary │ 10.10.10.40  │ 3306 │ 0           │ Master, Auth Error, Running │      │
├─────────┼──────────────┼──────┼─────────────┼─────────────────────────────┼──────┤
│ repl01  │ 10.10.10.212 │ 3306 │ 0           │ Auth Error, Running         │      │
└─────────┴──────────────┴──────┴─────────────┴─────────────────────────────┴──────┘
```
Fixed **Auth Error**

` maxscale[522]: Error during monitor permissions test for server 'primary': Query 'SHOW SLAVE STATUS;' failed: 'Access denied; you need (at least one of) the SUPER, SLAVE MONITOR privilege(s) for this operation'.`

```sql
GRANT SLAVE MONITOR ON *.* TO 'maxscale_monitor'@'10.10.10.%';
FLUSH PRIVILEGES;
```

**Show servers status:**
```shell
ubuntu@maxscale:~$ sudo maxctrl list servers
┌─────────┬──────────────┬──────┬─────────────┬─────────────────┬─────────────────┐
│ Server  │ Address      │ Port │ Connections │ State           │ GTID            │
├─────────┼──────────────┼──────┼─────────────┼─────────────────┼─────────────────┤
│ primary │ 10.10.10.40  │ 3306 │ 3           │ Master, Running │ 0-22040-9936180 │
├─────────┼──────────────┼──────┼─────────────┼─────────────────┼─────────────────┤
│ repl01  │ 10.10.10.212 │ 3306 │ 3           │ Slave, Running  │ 0-22040-9936179 │
└─────────┴──────────────┴──────┴─────────────┴─────────────────┴─────────────────┘
```



### Change user password
```sql
ALTER USER 'maxscale_monitor'@'10.10.10.%' IDENTIFIED BY 'maxscale_mon_s3cret';
FLUSH PRIVILEGES;
```
[1]: https://mariadb.com/kb/en/mariadb-maxscale-6-setting-up-mariadb-maxscale/ "Install MariaDB MaxScale"

[2]: https://dba.stackexchange.com/questions/151680/master-slave-mysql-error-in-replication "sss"



