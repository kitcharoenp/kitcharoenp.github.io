---
layout : post
title : "Install Zurmo on Ubuntu 22.04"
categories : [zurmo]
published : false
---


# Testing Database Connections

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

# Install php packages 7.2 up
```shell
$ sudo apt install php-apcu php-apcu-bc php-curl php-gd php-imap php-ldap php-mcrypt php-mbstring 
$ sudo apt install php-memcache php-memcached php-mysql php-soap
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


### Switch User
error messages
```
[error] [exception.CException] CException: CSecurityManager requires PHP mcrypt extension to be loaded
```
**Fixed:**
install `php-mcrypt.so`

#### [Install PHP 7.2-mcrypt Module on Ubuntu 18.04 LTS](https://geekrewind.com/install-php-7-2-mcrypt-module-on-ubuntu-18-04-lts/)

```
# Install Required PHP Dependencies to Install Mcrypt
$ sudo apt install php-dev libmcrypt-dev php-pear

# install PHP mcrypt module on Ubuntu using pecl channel
$ sudo pecl channel-update pecl.php.net
$ sudo pecl install mcrypt-1.0.6
```

output:
```
Build complete.
Don't forget to run 'make test'.

running: make INSTALL_ROOT="/tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6" install
Installing shared extensions:     /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr/lib/php/20190902/
running: find "/tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6" | xargs ls -dils
182809 1 drwxr-xr-x 3 root root      3 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6
183352 1 drwxr-xr-x 3 root root      3 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr
183963 1 drwxr-xr-x 3 root root      3 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr/lib
183839 1 drwxr-xr-x 3 root root      3 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr/lib/php
183353 1 drwxr-xr-x 2 root root      3 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr/lib/php/20190902
184084 1 -rwxr-xr-x 1 root root 219344 Jul 13 10:55 /tmp/pear/temp/pear-build-rootRX5fCO/install-mcrypt-1.0.6/usr/lib/php/20190902/mcrypt.so

Build process completed successfully
Installing '/usr/lib/php/20190902/mcrypt.so'
install ok: channel://pecl.php.net/mcrypt-1.0.6
configuration option "php_ini" is not set to php.ini location
You should add "extension=mcrypt.so" to php.ini
```

add `extension=mcrypt.so` in `[mcrypt]` on `/etc/php/7.2/apache2/php.ini`
```
# include the mcrypt.so extension in the php.ini file
[mcrypt]
...
extension=mcrypt.so

```

### [APC is not installed.](https://installati.one/install-php-apcu-ubuntu-22-04/)

```shell
$ sudo apt-get -y install php-apcu
```

### [Memcache extension is not installed](https://linux.how2shout.com/how-to-install-memcached-on-ubuntu-22-04-lts-server/)

```shell
$ sudo apt install memcached libmemcached-tools
$ sudo apt install php-memcached
$ sudo apt install php-memcache
```


### 7.2
* [error] [exception.ParseError] ParseError: syntax error, unexpected 'const' (T_CONST), expecting variable (T_VARIABLE) in /var/www/zurmo/yii/framework/web/helpers/CJSON.php:66

*  Function get_magic_quotes_gpc() is deprecated 