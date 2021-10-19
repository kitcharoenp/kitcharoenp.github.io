---
layout : post
title : "Nginx : Resolving upstream sent too big header"
categories : [nginx]
published : true
---
### 502 Bad Gateway error

**/var/log/nginx/error.log**
```log
06:50:11 [error] 861476#861476: *1 upstream sent too big header while reading response header from upstream,
```

### [Fix when Nginx is running in a proxy / reverse proxy mode][0]

```
...
server {
...
 proxy_busy_buffers_size   512k;
 proxy_buffers   4 512k;
 proxy_buffer_size   256k;
...
}
...
```



### [Dealing with proxy_pass and fastcgi buffers][0]

`The error states the issue. fastcgi_busy_buffers_size is larger than fastcgi_buffers.`

Append the following directives after `fastcgi_pass`:
```
...
location ~ [^/]\.php(/|$) {
...
    ## TUNE buffers to avoid error ##  
    fastcgi_buffers 16 32k;
    fastcgi_buffer_size 64k;
    fastcgi_busy_buffers_size 64k;
...
```

[0]: https://www.cyberciti.biz/faq/nginx-upstream-sent-too-big-header-while-reading-response-header-from-upstream/ "Nginx upstream sent too big header"