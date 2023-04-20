---
layout : post
title : "Install Zurmo on Ubuntu 20.04"
categories : [zurmo]
published : true
---
# Create a container

```shell
$ lxc launch ubuntu:20.04 zurmo2004
```

# Install packages
```shell
$ sudo apt update
$ sudo apt install apache2 # webserver
```

# Install php7.4  packages 
```shell
$ sudo apt install php7.4 php7.4-cgi php7.4-curl php7.4-dev php7.4-fpm php7.4-gd
$ sudo apt install php7.4-imap php7.4-json php7.4-mbstring php7.4-mysql php7.4-soap
$ sudo apt install php7.4-xml php7.4-xmlrpc
```

# Get sourecode 
```shell
$ rsync -avpP --exclude "localfiles"  /var/www/oamp00/public_html/zurmo  /home/oamp00

ubuntu@zurmo2004:/var/www/html$ ls -la
total 36
drwxr-xr-x 3 root   root       4 Apr 20 08:28 .
drwxr-xr-x 3 root   root       3 Apr 20 07:45 ..
-rw-r--r-- 1 root   root   10918 Apr 20 07:45 index.html
drwxrwxrwx 6 ubuntu ubuntu    11 Jan 26 04:14 zurmo
```

## Testing Database Connections

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
* PHP Fatal error:  Array and string offset access syntax with curly braces is no longer supported in /var/www/zurmo/yii/framework/utils/CFormatter.php on line 207

* [php] Trying to access array offset on value of type null

* [Troubleshooting](https://gitlab.com/kitcharoenp/zurmo/-/wikis/Zurmo-:-Troubleshooting-&-Tunning)

* https://freek.dev/1518-automatically-convert-your-code-to-php-74-syntax-using-rector

* Unknown named parameter `$offset`

* [error] [exception.TypeError] TypeError: count(): Argument #1 ($value) must be of type Countable|array, DropDownDependencyDerivedAttributeMetadata given
Fixed disable ~E_Waring & & ~E_NOTICE


### 7.2
* [error] [exception.ParseError] ParseError: syntax error, unexpected 'const' (T_CONST), expecting variable (T_VARIABLE) in /var/www/zurmo/yii/framework/web/helpers/CJSON.php:66

*  Function get_magic_quotes_gpc() is deprecated 