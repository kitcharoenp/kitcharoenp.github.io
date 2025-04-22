---
layout : post
title : "Install Zurmo on Ubuntu 24.04 with Php7.x"
categories : [zurmo]
published : false
---
# Create a container

```shell
$ lxc launch ubuntu:24.04 crm-api
```

# Install packages
```shell
$ sudo apt update
$ sudo apt install software-properties-common -y
$ sudo apt install apache2 # webserver
```

# [Install php7.4 packages](https://tecadmin.net/how-to-install-php-on-ubuntu-24-04/) 

```shell
# Add PHP Repository
$ sudo add-apt-repository ppa:ondrej/php -y

# Install PHP
$ sudo apt install php7.4 -y

# Install PHP Extensions
$ sudo apt install php7.4-cgi php7.4-curl php7.4-dev php7.4-fpm php7.4-gd
$ sudo apt install php7.4-imap php7.4-json php7.4-mbstring php7.4-mysql php7.4-soap
$ sudo apt install php7.4-xml php7.4-xmlrpc

# Memcache
$ sudo apt install php7.4-memcache php7.4-memcached
# APC
$ sudo apt install php7.4-apcu

# Fixed can not execute .php 
$ sudo apt install libapache2-mod-php7.4
```


# Get CRM sourecode 
```shell
$ rsync -avpP --exclude "localfiles"  /var/www/oamp00/public_html/zurmo  /home/oamp00

ubuntu@zurmo2004:/var/www/html$ ls -la
total 36
drwxr-xr-x 3 root   root       4 Apr 20 08:28 .
drwxr-xr-x 3 root   root       3 Apr 20 07:45 ..
-rw-r--r-- 1 root   root   10918 Apr 20 07:45 index.html
drwxrwxrwx 6 ubuntu ubuntu    11 Jan 26 04:14 zurmo
```

* https://www.rosehosting.com/blog/install-zurmo-crm-with-apache-mysql-and-php-on-an-ubuntu-vps/

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
$ sudo chmod -R 777 app/assets
$ sudo chmod -R 777 app/protected/runtime
$ sudo usermod -a -G www-data ubuntu
$ sudo chmod -R g+w /var/www/zurmo
```
Fixed `zurmo_rest_api` connection 



## writable

**Fixed:**
```
Failed Required Services
* The application.log runtime file is writable.
* The /minScript/cache runtime directory is writable.
* The debug.php file is not present.	FAIL
* /var/www/zurmo/app/protected/data is not writable.	FAIL
```
Change owner `/var/www/zurmo` as `www-data`
```shell
$  sudo chown -R www-data:www-data /var/www/zurmo

# add `ubuntu` to `www-data` for rw `/var/www/zurmo`
$ sudo usermod -a -G www-data ubuntu
```

**Fixed** : PHP Fatal error: Class `SecurableModule` not found

* Copy `perInstanceConfig.php` 
```shell
$ touch app/protected/config/perInstanceConfig.php
```

### Page Blank

**solution:** Edit `php.ini` of fpm

```shell
; edit /etc/php/7.4/fpm/php.ini
; or edit /etc/php/7.4/apache2/php.ini
; or edit /etc/php/7.4/apache2/php.ini

error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING

```

## Mcrypt

#### Mcrypt extension is not loaded.	FAIL
Install PHP mcrypt extension to fix **Error Messages:**

```
[error] [exception.CException] CException: CSecurityManager requires PHP mcrypt extension to be loaded
```
**Fixed:** : Install `php-mcrypt.so`

[Install PHP Mcrypt](https://itsubuntu.com/install-php-mcrypt-extension-on-ubuntu-22-04/)

```bash
# Install Required PHP Dependencies to Install Mcrypt
$ sudo apt install libmcrypt-dev

# install PHP mcrypt module on Ubuntu using pecl channel
$ sudo pecl install mcrypt-1.0.3 # 7.4
...
Build process completed successfully
Installing '/usr/lib/php/20190902/mcrypt.so'
install ok: channel://pecl.php.net/mcrypt-1.0.3
configuration option "php_ini" is not set to php.ini location
You should add "extension=mcrypt.so" to php.ini
..
```

`/etc/php/7.4/apache2/php.ini` or `/etc/php/7.4/fpm/php.ini` 
```
; List of headers files to preload, wildcard patterns allowed.
;ffi.preload=
;
extension=mcrypt.so
```

## APC

### [Install APC](https://installati.one/install-php-apcu-ubuntu-22-04/)

```shell
$ sudo apt install php7.4-apcu
...
NOTICE: Not enabling PHP 7.4 FPM by default.
NOTICE: To enable PHP 7.4 FPM in Apache2 do:
NOTICE: a2enmod proxy_fcgi setenvif
NOTICE: a2enconf php7.4-fpm
NOTICE: You are seeing this message because you have apache2 package installed.
...
```


## Memcache

### [Install Memcache extension](https://linux.how2shout.com/how-to-install-memcached-on-ubuntu-22-04-lts-server/)

**Fixed:** `Memcache` extension is not installed

```shell
$ sudo apt install memcached libmemcached-tools
$ sudo apt install php7.4-memcache php7.4-memcached
```



# FPM

**Cautions:** This cause `REST API` raised error `invalid username or password` because modified header is droped 

Install `php-fpm`
```shell
$ sudo apt install php-fpm 
```

### Disable the `mod_php` module and enaable the `mod_fpm` module

```bash
$ sudo a2dismod php7.4
$ sudo a2enconf php7.4-fpm
$ sudo a2enmod proxy_fcgi setenvif
```

### Disable the `mpm_prefork` module and enaable the `mpm_event` module

```bash
# disable mpm_prefork module
$ sudo a2dismod mpm_prefork

# enable the mpm_event module
$ sudo a2enmod mpm_event

# restart service
$ sudo service  php7.4-fpm restart
$ sudo service apache2 restart
```


### ERROR
* Javascript is not load

* `PHP Fatal error:  Uncaught Error: Class 'SecurableModule' not found in ...`

    **solution:**
    ```shell
    $ cd /var/www/html/zurmo/
    $ sudo chmod o+w app/assets
    $ sudo chmod o+w app/protected/runtime
    ```

* `PHP Notice:  Undefined property: WebApplication::$minScript`

    **solution:**
    ```shell
    $ cd /var/www/html/zurmo/
    $ sudo rm -rf app/protected/runtime/minScript/
    ```

* `[error] [php] Undefined variable: name`

    **solution:**
    ```shell
    # edit /etc/php/7.4/fpm/php.ini
    ...

   error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING

   ...
    ```
    **restart apache2**
    ```shell
    sudo systemctl restart apache2
    
    # for enable http2
    sudo service php7.4-fpm restart
    ```


### Signature Images
Check images fiel or update by using `Rsyn` to update files
* `quotation/images`
* `extension/signature_images/`

#### AH01071 Maximum execution time
`AH01071: Got error 'PHP message: PHP Fatal error:  Maximum execution time of 30 seconds`

```
; Maximum execution time of each script, in seconds
; http://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 300
```

### proxy_fcgi : (70007)The timeout specified has expired
```
 [proxy_fcgi:error] [pid 18643:tid 139852146222848] (70007)The timeout specified has expired:
```
** sudo vim /etc/apache2/sites-available/001-default.conf**

[Configuring FastCGI and PHP-FPM on Ubuntu 18.04](https://www.linode.com/docs/guides/how-to-install-and-configure-fastcgi-and-php-fpm-on-ubuntu-18-04/)
```
<IfModule mod_fcgid.c>
        Options +ExecCGI
        FcgidConnectTimeout 300
        AddType  application/x-httpd-php         .php
        AddHandler application/x-httpd-php .php
        Alias /php7-fcgi /usr/lib/cgi-bin/php7-fcgi
        ProxyPassMatch " ^/(.*\.php(/.*)?)$" "unix:/run/php/php7.2-fpm_example.com.sock|fcgi://localhost/var/www/zurmo/" connectiontimeout=5 enablereuse=on keepalive=on retry=0 timeout=300 ttl=300
</IfModule>

```

### [Temporary failure in name resolution](https://discourse.ubuntu.com/t/problem-with-dns-resolution-within-a-container/48391)

Uninstalled the `systemd-resolved` package
```
```


### Other
 * [Rector](https://freek.dev/1518-automatically-convert-your-code-to-php-74-syntax-using-rector)

 * [Gilab Wiki](https://gitlab.com/kitcharoenp/zurmo/-/wikis/Zurmo-:-Troubleshooting-&-Tunning)