---
layout : post
title : "Enable HTTP/2 on Ubuntu 20.04"
categories : [apache2, php]
published : true
---
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