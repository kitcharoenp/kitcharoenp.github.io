---
layout : post
title : "Install Zurmo on Ubuntu 22.04"
categories : [zurmo]
published : true
---
# Test connect Database

```shell
$ mysql -u db_user -pdb_password --host=db_ip_address
ERROR 2002 (HY000): Can't connect to MySQL server on 'db_ip_address' (115)
```
[Fixed: ](https://mariadb.com/kb/en/configuring-mariadb-for-remote-client-access/#finding-the-defaults-file) To enable MariaDB to listen to remote connections, you need to edit your defaults file.

### Editing the Defaults File
If bind-address is bound to `127.0.0.1` (localhost), one can't connect to the MariaDB server from other hosts or from the same host over TCP/IP on a different interface than the loopback (127.0.0.1). 
```
 [mysqld]
    ...
    #skip-networking
    ...
    #bind-address = <some ip-address>
    ...
```

# Initial Setup

* Create `assets` and `runtime` folder and `chmod`

```shell
$ sudo chmod o+w app/assets
$ sudo chmod o+w app/protected/runtime
```
**Fixed** : PHP Fatal error: Class `SecurableModule` not found

* Copy `perInstanceConfig.php` 
```shell
$ touch app/protected/config/perInstanceConfig.php
```

# Install php
```shell
$ sudo apt install php-fpm 
```



### ERROR
PHP Fatal error:  Array and string offset access syntax with curly braces is no longer supported in /var/www/zurmo/yii/framework/utils/CFormatter.php on line 207



* [Troubleshooting](https://gitlab.com/kitcharoenp/zurmo/-/wikis/Zurmo-:-Troubleshooting-&-Tunning)