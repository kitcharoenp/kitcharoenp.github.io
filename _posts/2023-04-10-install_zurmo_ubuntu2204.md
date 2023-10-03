---
layout : post
title : "Install Zurmo on Ubuntu 22.04"
categories : [zurmo]
published : true
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


# Create a container

```shell
$ lxc launch ubuntu:22.04 crm-app-y0
```

# Install packages
```shell
$ sudo apt update
$ sudo apt install apache2 # webserver
```

### Install php8.1  packages 
```shell
$ sudo apt install php8.1 php8.1-cgi php8.1-curl php8.1-dev php8.1-fpm php8.1-gd
$ sudo apt install php8.1-imap php8.1-mbstring php8.1-mysql php8.1-soap
$ sudo apt install php8.1-xml php8.1-xmlrpc
# Fixed execute .php 
$ sudo apt install libapache2-mod-php8.1

```

### Install PHP mcrypt extension
**Error Messages:**

```
[error] [exception.CException] CException: CSecurityManager requires PHP mcrypt extension to be loaded
```
**Fixed:** : Install `php-mcrypt.so`

#### [Install PHP Mcrypt](https://itsubuntu.com/install-php-mcrypt-extension-on-ubuntu-22-04/)

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

#### Activate the extension in  `/etc/php/8.1/fpm/php.ini` 

```
[mcrypt]
extension=mcrypt.so
```

### [Install Memcache extension](https://linux.how2shout.com/how-to-install-memcached-on-ubuntu-22-04-lts-server/)

**Fixed:** `Memcache` extension is not installed

```shell
$ sudo apt install memcached libmemcached-tools
$ sudo apt install php-memcached
$ sudo apt install php-memcache
```


### [Install APC](https://installati.one/install-php-apcu-ubuntu-22-04/)



```shell
$ sudo apt -y install php8.1-apcu
```

```shell
$ sudo apt-get -y install gcc make autoconf libc-dev pkg-config
$ sudo pecl install apcu

...
install ok: channel://pecl.php.net/apcu-5.1.22
configuration option "php_ini" is not set to php.ini location
You should add "extension=apcu.so" to php.ini
...

```

#### Activate the extension in  `/etc/php/8.1/fpm/php.ini` 

```
[mcrypt]
extension=apcu.so
```


### Restart service
```bash
$ sudo service php8.1-fpm restart
$ sudo service apache2 restart
```

# Enable an Apache MPM

### Disable the `mod_php` module and enaable the `mod_fpm` module

```bash
$ sudo a2dismod php8.1
$ sudo a2enconf php8.1-fpm
$ sudo a2enmod proxy_fcgi
```

### Disable the `mpm_prefork` module and enaable the `mpm_event` module

```bash
# disable mpm_prefork module
$ sudo a2dismod mpm_prefork

# enable the mpm_event module
$ sudo a2enmod mpm_event

# restart service
$ sudo service  php8.1-fpm restart
$ sudo service apache2 restart
```

### [`php.ini` Tunning](https://haydenjames.io/php-8-compatibility-check-and-performance-tips/)

```
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING

memory_limit = 1024M
output_buffering = Off



[opcache]
; Determines if Zend OPCache is enabled
opcache.enable=1

; Determines if Zend OPCache is enabled for the CLI version of PHP
;opcache.enable_cli=0

; The OPcache shared memory storage size.
opcache.memory_consumption=256

; The amount of memory for interned strings in Mbytes.
opcache.interned_strings_buffer=16

; The maximum number of keys (scripts) in the OPcache hash table.
; Only numbers between 200 and 100000 are allowed.
opcache.max_accelerated_files=10000

; The maximum percentage of "wasted" memory until a restart is scheduled.
opcache.max_wasted_percentage=10

; When this directive is enabled, the OPcache appends the current working
; directory to the script key, thus eliminating possible collisions between
; files with the same name (basename). Disabling the directive improves
; performance, but may break existing applications.
;opcache.use_cwd=1

; When disabled, you must reset the OPcache manually or restart the
; webserver for changes to the filesystem to take effect.
opcache.validate_timestamps=0

; How often (in seconds) to check file timestamps for changes to the shared
; memory storage allocation. ("1" means validate once per second, but only
; once per request. "0" means always validate)
opcache.revalidate_freq=60

```
* [How to Turn Off or Disable Output Buffering in PHP with php.ini](https://webhostinggeeks.com/howto/disable-output-buffering-php-ini/)



## Setup Application

* Pull a source code from git to `/var/www/zurmo` 
* Set the owner of the `/var/www/zurmo`  as `www-data`
* Create and set permission the `assets` and the `runtime` directory 

    ```shell
    $ sudo chmod o+w app/assets
    $ sudo chmod o+w app/protected/runtime
    ```


* Restart the `apache2` service
    ```shell
    sudo systemctl restart apache2

    # for enable http2
    sudo service php8.1-fpm restart
    ```


####  Array and string offset access syntax with curly braces is no longer supported 

Error messages:
```
PHP Fatal error:  Array and string offset access syntax with curly braces is no longer supported 
```

**Solution**
replace `{}` with `[]` on code




#### [Apache2 AH00558](https://www.digitalocean.com/community/tutorials/apache-configuration-error-ah00558-could-not-reliably-determine-the-server-s-fully-qualified-domain-name)

**messages:**
```
AH00558: apache2: Could not reliably determine the server's fully qualified domain name, using 10.10.10.123. Set the 'ServerName' directive globally to suppress this message
```

**solution:**
append `etc/apache2/apache2.conf` with
```
ServerName 127.0.0.1
```