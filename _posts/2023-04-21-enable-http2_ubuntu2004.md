---
layout : post
title : "Enable HTTP/2 on Ubuntu 20.04"
categories : [apache2, php]
published : true
---
[HTTP/2](https://httpd.apache.org/docs/2.4/howto/http2.html) is the evolution of the world's most successful application layer protocol, HTTP. It focuses on making more efficient use of network resources. It does not change the fundamentals of HTTP, the semantics. There are still request and responses and headers and all that. So, if you already know HTTP/1, you know 95% about HTTP/2 as well.

### Prerequisites
```shell
sudo apt update
sudo apt dist-upgrade 
# install apache2
sudo apt install apache2

# check status 
$ sudo service apache2 status
..
Active: active (running) since Thu 2023-05-11 04:16:10 UTC; 16s ago
   Docs: https://httpd.apache.org/docs/2.4/
..

# check supports HTTP
$ curl --head localhost
..
HTTP/1.1 200 OK
Date: Thu, 11 May 2023 04:20:35 GMT
Server: Apache/2.4.41 (Ubuntu)
Last-Modified: Thu, 11 May 2023 04:16:07 GMT
ETag: "2aa6-5fb633f5f93d2"
Accept-Ranges: bytes
Content-Length: 10918
Vary: Accept-Encoding
Content-Type: text/html
..
```

### Disable the mod_php module

```shell
sudo apt-get install php7.4-fpm
sudo a2dismod php7.4
sudo a2enconf php7.4-fpm
sudo a2enmod proxy_fcgi
```

### Enable an Apache MPM

```shell
# disable mpm_prefork module
sudo a2dismod mpm_prefork

# enable the mpm_event module
sudo a2enmod mpm_event
```

### Enable HTTP/2 

```shell
# enable and load SSL and HTTP/2 modules
sudo a2enmod ssl
sudo a2enmod http2

#  activate these new modules,
sudo systemctl restart apache2
```

### Edit Apache configuration. 

**/etc/apache2/sites-available/default-ssl.conf**
```shell
#   A self-signed (snakeoil) certificate can be created by installing
#   the ssl-cert package. See
#   /usr/share/doc/apache2/README.Debian.gz for more info.
#   If both key and certificate are stored in the same file, only the
#   SSLCertificateFile directive is needed.
SSLCertificateFile	/etc/ssl/certs/ssl-cert-snakeoil.pem
SSLCertificateKeyFile /etc/ssl/private/ssl-cert-snakeoil.key


SSLProtocol all -SSLv3 -TLSv1 -TLSv1.1

# Enable HTTP/2
Protocols h2 http/1.1

```

### Enable Site

```shell
# enable and load SSL and HTTP/2 modules
sudo a2ensite default-ssl.conf

#  activate these new modules,
sudo systemctl restart apache2
sudo service php7.4-fpm restart
```


### Reference
 * [howtoforge](https://www.howtoforge.com/how-to-enable-http-2-in-apache/)