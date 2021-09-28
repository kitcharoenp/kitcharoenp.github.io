---
layout : post
title : "MMariaDB MaxScale"
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

```
CREATE USER 'maxscale_monitor'@'10.10.10.%' IDENTIFIED BY 'maxscale_mon_s3cret';
GRANT REPLICATION CLIENT on *.* to 'maxscale_monitor'@'10.10.10.%';
FLUSH PRIVILEGES;
```

change user password on mysql
ALTER USER 'maxscale_monitor'@'10.10.10.%' IDENTIFIED BY 'maxscale_mon_s3cret';
FLUSH PRIVILEGES;

### Configuring the services and listeners

The user and password parameters define the credentials the service uses to populate user authentication data.



**mysql.procs_priv**
> warning: Using old user account query due to insufficient privileges. To avoid this warning, give the service user of 'Read-Only-Service' access to the 'mysql.procs_priv'-table.

```
GRANT SELECT ON mysql.proxies_priv TO 'maxscale'@'maxscalehost';
```

### Starting MaxScale

### Checking MaxScale status with MaxCtrl

[1]: https://mariadb.com/kb/en/mariadb-maxscale-6-setting-up-mariadb-maxscale/ "Install MariaDB MaxScale"

[2]: https://dba.stackexchange.com/questions/151680/master-slave-mysql-error-in-replication "sss"



