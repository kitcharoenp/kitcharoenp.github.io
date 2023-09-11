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


# Install php8.1  packages 
```shell
$ sudo apt install php8.1 php8.1-cgi php8.1-curl php8.1-dev php8.1-fpm php8.1-gd
$ sudo apt install php8.1-imap php8.1-json php8.1-mbstring php8.1-mysql php8.1-soap
$ sudo apt install php8.1-xml php8.1-xmlrpc
# Fixed execute .php 
$ sudo apt install libapache2-mod-php8.1

# 
$ sudo apt install php8.1-memcache php8.1-memcached php8.1-opcache
```

### Disable the mod_php module

```bash
$ sudo apt-get install php8.1-fpm
$ sudo a2dismod php8.1
$ sudo a2enconf php8.1-fpm
$ sudo a2enmod proxy_fcgi
```

### Enable an Apache MPM

```bash
# disable mpm_prefork module
$ sudo a2dismod mpm_prefork

# enable the mpm_event module
$ sudo a2enmod mpm_event

# restart service
$ sudo service  php8.1-fpm restart
$ sudo service apache2 restart
```


# Initial Setup

* Create `assets` and `runtime` folder and `chmod`

```shell
$ sudo chmod o+w app/assets
$ sudo chmod o+w app/protected/runtime
```


## ERROR


### Install PHP mcrypt extension
**Error Messages:**

```
[error] [exception.CException] CException: CSecurityManager requires PHP mcrypt extension to be loaded
```
**Fixed:** : Install `php-mcrypt.so`

#### [Install PHP Mcrypt Extension On Ubuntu 22.04](https://itsubuntu.com/install-php-mcrypt-extension-on-ubuntu-22-04/)

```bash
# Install Required PHP Dependencies to Install Mcrypt
$ sudo apt install gcc make autoconf libc-dev pkg-config libmcrypt-dev php-pear php-dev

# install PHP mcrypt module on Ubuntu using pecl channel
$ sudo pecl channel-update pecl.php.net
$ sudo pecl update-channels
$ sudo pecl install mcrypt-1.0.6
```

**Output:**
```console
downloading mcrypt-1.0.6.tgz ...
Starting to download mcrypt-1.0.6.tgz (27,062 bytes)
.........done: 27,062 bytes
6 source files, building

....

Build process completed successfully
Installing '/usr/lib/php/20210902/mcrypt.so'
install ok: channel://pecl.php.net/mcrypt-1.0.6
configuration option "php_ini" is not set to php.ini location
You should add "extension=mcrypt.so" to php.ini
```

#### activate the extension in  `/etc/php/8.1/fpm/php.ini` 

```
[mcrypt]
extension=mcrypt.so
```

#### Restart service
```bash
$ sudo service php8.1-fpm restart
$ sudo service apache2 restart
```



###  Array and string offset access syntax with curly braces is no longer supported 

Error messages:
```
PHP Fatal error:  Array and string offset access syntax with curly braces is no longer supported 
```

**Solution**
replace `{}` with `[]` on code


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

### [AH00558](https://www.digitalocean.com/community/tutorials/apache-configuration-error-ah00558-could-not-reliably-determine-the-server-s-fully-qualified-domain-name)

**messages:**
```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 10.10.10.123. Set the 'ServerName' directive globally to suppress this message
```

**solution:**
append `etc/apache2/apache2.conf` with
```
ServerName 127.0.0.1
```

### 7.2
* [error] [exception.ParseError] ParseError: syntax error, unexpected 'const' (T_CONST), expecting variable (T_VARIABLE) in /var/www/zurmo/yii/framework/web/helpers/CJSON.php:66

*  Function get_magic_quotes_gpc() is deprecated 



* [php] Trying to access array offset on value of type null

* [Troubleshooting](https://gitlab.com/kitcharoenp/zurmo/-/wikis/Zurmo-:-Troubleshooting-&-Tunning)

* https://freek.dev/1518-automatically-convert-your-code-to-php-74-syntax-using-rector

* Unknown named parameter `$offset`

* [error] [exception.TypeError] TypeError: count(): Argument #1 ($value) must be of type Countable|array, DropDownDependencyDerivedAttributeMetadata given
Fixed disable ~E_Waring & & ~E_NOTICE