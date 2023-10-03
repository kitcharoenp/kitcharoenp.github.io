---
layout : post
title : "PHP-FPM Tuning"
categories : [php]
published : false
---

PHP-FPM is a FastCGI Process Manager an alternative for PHP.

### Calculate Values for PHP-FPM Children

Determine the memory used by each (PHP-FPM) child process:
```shell

$ ps -ylC php-fpm8.1 --sort:rss
S   UID     PID    PPID  C PRI  NI   RSS    SZ WCHAN  TTY          TIME CMD
S     0     193       1  0  80   0 18168 55600 -      ?        00:00:05 php-fpm7.4
S    33    1511     193  0  80   0 43256 75854 -      ?        00:00:09 php-fpm7.4
S    33    1396     193  0  80   0 48236 77400 -      ?        00:00:21 php-fpm7.4
S    33    1426     193  0  80   0 49844 77460 -      ?        00:00:13 php-fpm7.4
```
The RSS column shows non-swapped physical memory usage by PHP-FPM processes in kilo Bytes. Here we have about **48MB** RAM is used in average by each PHP-FPM child process.

> `rss` : resident set size, the non-swapped physical memory that a task has used (in kiloBytes).

Appropriate value for `pm.max_children` can be calculated as:

```
pm.max_children = Total Memory Available / (50MB~90MB)

10000MB / 40MB = 250 pm.max_children
```
We are about to allow 10GB RAM for PHP-FPM processing. So 10000/40 = 250

By default the `pm` value will be set to `dynamic` . We will change the pm value to `ondemand` to the spare servers will get created automatically according to the demand.

> `Ondemand` – No children are created at startup. Children will be forked when new requests will connect. This will cause some delay on site first load

edit `/etc/php/8.1/fpm/pool.d/www.conf`
```
pm = ondemand
pm.max_children = 250
pm.max_requests = 250
```

Test the validity of your php-fpm configuration syntax
```shell
$ sudo php-fpm8.1 -t
[23-May-2023 17:02:38] NOTICE: configuration file /etc/php/7.4/fpm/php-fpm.conf test is successful
$ sudo service php8.1-fpm restart 
```




### Reference
* [PHP-FPM Configuration](https://www.cloudbooklet.com/best-php-fpm-configuration-easy-and-simple-calculation/)
* [Maximizing The Potential Of PHP-FPM](https://marketsplash.com/tutorials/php/php-fpm/)


### [Using OPcache](https://www.arubacloud.com/tutorial/how-to-install-and-configure-php-opcache-on-ubuntu-18-04.aspx)

> OPcache is an Apache module for the PHP interpreter that allows to increase its performance by storing precompiled scripts in the shared memory. In this way, PHP does not have to load and interpret the same script at every request.  Normally, PHP compiles your application and then executes it every time you do a request on your application.

```
[opcache]
; Determines if Zend OPCache is enabled
opcache.enable=1

; The OPcache shared memory storage size.
opcache.memory_consumption=256

; The amount of memory for interned strings in Mbytes
opcache.interned_strings_buffer=16

; The maximum number of keys (scripts) in the OPcache hash table.
; Only numbers between 200 and 100000 are allowed.
opcache.max_accelerated_files=10000

; The maximum percentage of "wasted" memory until a restart is scheduled.
opcache.max_wasted_percentage=10

; How often (in seconds) to check file timestamps for changes to the shared
; memory storage allocation. ("1" means validate once per second, but only
; once per request. "0" means always validate)
opcache.revalidate_freq=60

opcache.validate_timestamps=0

; If enabled, a fast shutdown sequence is used for the accelerated code
opcache.fast_shutdown=1
```

```
$ sudo service apache2 reload
```

### Reference

* [Integrate PHP Opcache](https://www.cloudways.com/blog/integrate-php-opcache/)

* [Opcache Configuration](https://tideways.com/profiler/blog/fine-tune-your-opcache-configuration-to-avoid-caching-suprises)





* https://tideways.com/profiler/blog/an-introduction-to-php-fpm-tuning 

* https://sourcecloud.co.uk/blogs/performance-tuning-nginx-and-php-fpm-for-xenforo/

* https://www.digitalocean.com/community/tutorials/php-fpm-nginx

* https://blog.jitendrapatro.me/tuning-apache-php-and-mysql-for-better-performance/

Apache Bench