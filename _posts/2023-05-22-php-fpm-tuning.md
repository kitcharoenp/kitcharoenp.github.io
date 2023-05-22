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

$ ps -ylC php-fpm7.4 --sort:rss
S   UID     PID    PPID  C PRI  NI   RSS    SZ WCHAN  TTY          TIME CMD
S     0     193       1  0  80   0 18168 55600 -      ?        00:00:05 php-fpm7.4
S    33    1511     193  0  80   0 43256 75854 -      ?        00:00:09 php-fpm7.4
S    33    1396     193  0  80   0 48236 77400 -      ?        00:00:21 php-fpm7.4
S    33    1426     193  0  80   0 49844 77460 -      ?        00:00:13 php-fpm7.4
```
The RSS column shows non-swapped physical memory usage by PHP-FPM processes in kilo Bytes. Here we have about **48MB** RAM is used in average by each PHP-FPM child process.

* `rss` : resident set size, the non-swapped physical memory that a task has used (in kiloBytes).

Appropriate value for `pm.max_children` can be calculated as:

```
Maximum RAM can be used by PHP-FPM / RAM consumed by each child = Maximum children value for PHP-FPM
```
We are about to allow 10GB RAM for PHP-FPM processing. So 10000/40 = 250

By default the `pm` value will be set to `dynamic` . We will change the pm value to `ondemand` to the spare servers will get created automatically according to the demand.

edit `/etc/php/7.4/fpm/pool.d/www.conf`
```
pm = ondemand
pm.max_children = 250
pm.max_requests = 250
```


### Reference
* [PHP-FPM Configuration](https://www.cloudbooklet.com/best-php-fpm-configuration-easy-and-simple-calculation/)

* https://gist.github.com/dublado/58c7281f9d062fa3396de05ad564e6d7

* https://tideways.com/profiler/blog/an-introduction-to-php-fpm-tuning 

* https://sourcecloud.co.uk/blogs/performance-tuning-nginx-and-php-fpm-for-xenforo/

* https://www.digitalocean.com/community/tutorials/php-fpm-nginx