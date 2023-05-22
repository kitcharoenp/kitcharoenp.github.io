---
layout : post
title : "Nginx Performance Tuning"
categories : [nginx]
published : false
---

## Adjusting the Nginx Configuration to Use SSL
```shell
   $ sudo nano /etc/nginx/nginx.conf
```

### Logging Settings
```
##
# Logging Settings
##

   # access_log /var/log/nginx/access.log;
   error_log /var/log/nginx/error.log;

   # enabling buffering to the log. Once the buffer size reaches its limit, 
   # Nginx writes buffer content to log. 
   # access_log /var/log/nginx/access.log main buffer=16k;

   # disable the access log
   access_log off;
   
```

### Gzip Settings
uncomment and modify the gzip
```
   ##
   # Gzip Settings
   ##

   gzip on;

   gzip_vary on;
   gzip_proxied any;
   gzip_comp_level 6;
   gzip_buffers 16 8k;
   gzip_http_version 1.1;
   gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```


### Reference
* https://gist.github.com/leandromoreira/1c655189b8fae2e24175
* https://www.tweaked.io/guide/nginx-proxying/
* https://vitux.com/nginx-performance-tuning/
* https://gist.github.com/denji/8359866
* https://www.freecodecamp.org/news/powerful-ways-to-supercharge-your-nginx-server-and-improve-its-performance-a8afdbfde64d/
* https://www.makeuseof.com/ways-improve-nginx-performance-linux/
* https://www.nginx.com/blog/avoiding-top-10-nginx-configuration-mistakes/#upstream-groups
* https://www.makeuseof.com/ways-improve-nginx-performance-linux/