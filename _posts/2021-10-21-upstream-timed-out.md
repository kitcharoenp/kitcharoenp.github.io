---
layout : post
title : "Nginx : Upstream timed out (110:"
categories : [nginx]
published : true
---

**Error:**

```
upstream timed out (110: Connection timed out) while reading response header from upstream
```

### What causes

* > The upstream timeout error generally triggers when the upstream takes too much to answer the request and NGINX thinks the upstream already failed in processing the request. \[[0]\]

* `proxy_read_timeout` – Defines a timeout for reading a response from the proxied server. Default is **60** seconds.


### Solution

```
...

location ~ / {
  ...  
  proxy_connect_timeout      140;
  proxy_send_timeout         180;
  proxy_read_timeout         180;
  ...
}

...
```

[0]: https://bobcares.com/blog/nginx-upstream-timed-out-error/ "Nginx “upstream timed out”"