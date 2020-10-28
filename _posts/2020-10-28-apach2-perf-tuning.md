---
layout: post
title: "Apache Performance Tuning"
published: false
categories: [server]
---

### Hardware and Operating System Issues
> The single biggest hardware issue affecting webserver performance is RAM. A webserver should never ever have to swap, as swapping increases the latency of each request beyond a point that users consider "fast enough". This causes users to hit stop and reload, further increasing the load. You can, and should, control the `MaxRequestWorkers` setting so that your server does not spawn so many children that it starts swapping. [1][1]

### Discover usage statistics
```shell
$ ps -eo pcpu,pid,user,args | sort -k 1 -r | head -20
...
$ echo [PID]  [MEM]  [PATH] &&  ps aux | awk '{print $2, $4, $11}' | sort -k2rn | head -n 20
```

### List Module
```
$ sudo apache2ctl -M
Loaded Modules:
 core_module (static)
 so_module (static)
 watchdog_module (static)
 http_module (static)
 log_config_module (static)
 logio_module (static)
 ...
```

### Increase Memcached Memory
Memcached is a high performance distributed memory caching system. And it is mainly used to speed up web applications by reducing the database load.
In **/etc/memcached.conf:** find the line
```
...
# Start with a cap of 64 megs of memory. It's reasonable, and the daemon default
# Note that the daemon will grow to this size, but does not start out holding this much
# memory
-m 64 # change this value
...
```

### Apache MPM event
[**MPM Event**][4]: The event Multi-Processing Module (MPM) is designed to allow more requests to be served simultaneously by passing off some processing work to the listeners threads, freeing up the worker threads to serve new requests.

changing the default multi-processing module from `pre-fork` to `event`

```shell
# stop the Apache HTTP service
$ sudo systemctl stop apache2

# disable the PHP 7.x module connection between PHP and Apache HTTP
$ sudo a2dismod php7.x

# disable Pre-fork module
$ sudo a2dismod mpm_prefork

# enable the  Event MPM module
$ sudo a2enmod mpm_event
```

### [PHP-FPM][5]
PHP-FPM is the FastCGI Process Manager for PHP. The FastCGI protocol is based on the Common Gateway Interface (CGI), a protocol that sits between applications and web servers like Apache HTTP.

using the `PHP-FPM` process manager to handle PHP code instead of the classic` mod_php` in Apache HTTP



[1]: https://httpd.apache.org/docs/2.4/misc/perf-tuning.html "Hardware and Operating System Issues"
[2]: https://www.digitalocean.com/community/tutorials/how-to-configure-apache-http-with-mpm-event-and-php-fpm-on-ubuntu-18-04 "Configure Apache HTTP with MPM Event"
[3]: https://www.thomas-krenn.com/en/wiki/Apache_Performance_Tuning_Options "Apache Performance Tuning Options"
[4]: https://httpd.apache.org/docs/2.4/mod/event.html "Apache MPM event"
[5]: https://php-fpm.org/ "PHP-FPM"
