---
layout : post
title : "Install Zurmo on Ubuntu 20.04 with Php7.2 or Php7.4"
categories : [zurmo]
published : false
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
# Fixed execute .php 
$ sudo apt install libapache2-mod-php7.4
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
$ sudo chmod o+w app/assets
$ sudo chmod o+w app/protected/runtime
```

### Change owner `/var/www/zurmo` as `www-data`

**Fixed:** permission write on `/var/www/zurmo/app/runtimes`
```bash
$  sudo chown -R www-data:www-data /var/www/zurmo

# add `ubuntu` to `www-data` for rw `/var/www/zurmo`
$ sudo usermod -a -G www-data ubuntu
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

### Page Blank

**solution:** Edit `php.ini` of fpm
```shell
; edit /etc/php/7.4/fpm/php.ini
;
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING

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



### Other
 * [Rector](https://freek.dev/1518-automatically-convert-your-code-to-php-74-syntax-using-rector)

 * [Gilab Wiki](https://gitlab.com/kitcharoenp/zurmo/-/wikis/Zurmo-:-Troubleshooting-&-Tunning)