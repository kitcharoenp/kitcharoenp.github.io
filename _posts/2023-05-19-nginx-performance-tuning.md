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

### Content Compression : [Gzip Settings](https://docs.nginx.com/nginx/admin-guide/web-server/compression/)
Uncomment and modify the gzip setting in the configuration file, located at `/etc/nginx/nginx.conf`. 
```
   ##
   # Gzip Settings
   ##

   gzip on;

   gzip_vary on;
   gzip_proxied no-cache no-store private expired auth;
   gzip_comp_level 6;
   gzip_buffers 16 8k;

   # Sets the minimum HTTP version of a request required to compress a response.
   gzip_http_version 1.1;

   gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
```

### [Caching](https://tecadmin.net/boosting-websites-performance-with-nginx-caching/) 

**Benefits of Nginx Caching** : Improved load times, Reduced server load, Scalability, Enhanced SEO

1. **Configure Nginx for Caching**
Use these directives to set microcaching with the path at `/tmp/cache_nginx` with 100MB cache with a max size of 1GB of cache folder that updates cache in the background. 
edit the configuration file, located at `/etc/nginx/nginx.conf`. 
```
proxy_cache_path /tmp/cache_nginx levels=1:2 keys_zone=crm_proxy_cache:100m max_size=1g inactive=60m use_temp_path=off;
```
* **keys_zone=crm_proxy_cache:60m:** Cache zone and its size. 
* **inactive=60m:** Time after which unused cached content is removed. 
* **use_temp_path=off:** Disable the use of a temporary path for cache storage.

2. **Creating Microcaching Snippet**

   ```shell
   $ sudo nano /etc/nginx/snippets/proxy-cache-params.conf
   ```
   Within this file will look like the following:
   ```
   ## Microcaching ##
   proxy_cache_key "$scheme$request_method$host$request_uri";

   proxy_cache_methods GET HEAD;
   proxy_cache_valid any 1m;
   proxy_cache_valid 200 30m;
      
   # Cache locking: Cache locking can help prevent multiple simultaneous requests 
   # for the same uncached content, known as the “thundering herd” problem.
   proxy_cache_lock on;

   proxy_cache_bypass $http_cache_control;
   proxy_cache_use_stale updating error timeout http_500 http_502 http_503 http_504;

   add_header X-Proxy-Cache $upstream_cache_status;
   ```

* **proxy_cache_valid 200 30m:** Cache successful responses (HTTP 200) for 30 minutes.
* **add_header X-Proxy-Cache $upstream_cache_status:** Add a header to display the cache status in responses.

3. **Tunning Parameters**
   ```shell
   $ sudo nano /etc/nginx/snippets/tuning-params.conf
   ```
   Within this file will look like the following:
   ```
   proxy_set_header Upgrade $http_upgrade;
   proxy_set_header Connection $connection_upgrade;
   
   proxy_ignore_headers Set-Cookie;
   proxy_ignore_headers vary X-Accel-Expires Expires Cache-Control  Set-Cookie;

   proxy_hide_header vary;
   proxy_hide_header Upgrade;

   proxy_buffering on;

   proxy_connect_timeout      90;:
   proxy_read_timeout         90;
   proxy_send_timeout         300;
   send_timeout               300;

   client_max_body_size 10m;
   client_body_buffer_size    128k;
   client_body_timeout 60;
   ```

4. **Add Caching and Tunning to Server Block** open your site’s server block configuration file, usually located at `/etc/nginx/sites-available/default.` 

   ```
   #
   proxy_pass https://backend_crm;
   ...

   ## Microcaching ##
   proxy_cache crm_proxy_cache;

   # include Microcaching config params
   include snippets/proxy-cache-params.conf;

   # Intercept Link header and initiate requested Pushes
   http2_push_preload on;

   # include tunning config params
   include snippets/tuning-params.conf;

   ```


### Reference
* [Nginx Caching](https://www.oreilly.com/library/view/nginx-cookbook/9781492049098/ch04.html)
* [Cache Content in NGINX](https://www.tecmint.com/cache-content-with-nginx/)
* [Powerful ways to supercharge your NGINX](https://www.freecodecamp.org/news/powerful-ways-to-supercharge-your-nginx-server-and-improve-its-performance-a8afdbfde64d/)
* [Make your backend more reliable using Nginx caching proxy](https://www.sheshbabu.com/posts/nginx-caching-proxy/)

