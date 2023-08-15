---
layout : post
title : "Checkmk Apache_status Activate Plugin"
categories : [monitor, checkmk]
published : true
---


> The `mod_status` module is an Apache module that allows users to access highly detailed information about Apache’s performance on a plain HTML page.

### [Enable mod_status](https://www.tecmint.com/ubuntu-apache-mod_status/)

```bash
$ ls /etc/apache2/mods-enabled/ | grep status
status.conf
status.load
```

> By default, Apache ships with the mod_status module already enabled. Ensure that the `status.conf` and `status.load` files are present. If not, you need to enable `mod_status` module by invoking the command:


```bash
$ sudo a2enmod status
```

### Configure mod_status

```bash
$ sudo vim /etc/apache2/mods-enabled/status.conf 
```
![checkmk-activate-apache-status-plugin-01](/assets/img/2023-08/checkmk-activate-apache-status-plugin-01.png)

Set the `Require ip` directive to reflect the IP address of the machine that you will be accessing the server from. **For CheckMK `apache_status` plugin use default directive.**

### Check Apache Server Status
Display only Response Headers in `curl`:

```bash
$ curl --head http://localhost:8080/server-status
HTTP/1.1 200 OK
Date: Tue, 15 Aug 2023 03:48:53 GMT
Server: Apache/2.4.18 (Ubuntu)
Vary: Accept-Encoding
Content-Length: 27178
Content-Type: text/html; charset=ISO-8859-1
```

Port number `8080` is used for web servers.


```bash
$ curl  http://localhost:8080/server-status | less
```

**output:**
```console
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html><head>
<title>Apache Status</title>
</head><body>
<h1>Apache Server Status for localhost (via ::1)</h1>

<dl><dt>Server Version: Apache/2.4.18 (Ubuntu) OpenSSL/1.0.2g</dt>
<dt>Server MPM: prefork</dt>
<dt>Server Built: 2019-08-26T13:43:29
</dt></dl><hr /><dl>
<dt>Current Time: Tuesday, 15-Aug-2023 10:52:34 +07</dt>
<dt>Restart Time: Saturday, 12-Aug-2023 04:13:03 +07</dt>
<dt>Parent Server Config. Generation: 5</dt>
<dt>Parent Server MPM Generation: 4</dt>
<dt>Server uptime:  3 days 6 hours 39 minutes 31 seconds</dt>
<dt>Server load: 0.90 0.75 0.77</dt>
<dt>Total accesses: 55664 - Total Traffic: 507.4 MB</dt>
<dt>CPU Usage: u1632.1 s160.9 cu0 cs0 - .633% CPU load</dt>
<dt>.197 requests/sec - 1878 B/second - 9.3 kB/request</dt>
<dt>28 requests currently being processed, 33 idle workers</dt>
</dl><pre>_RWRR_.RR_R_R______R_.RR_.__R._WWW.K_RW_._KW.__K_R.W_W_W.R..._.R
_.._.__._........_.._.W......_....._............................
................................................................
........</pre>
<p>Scoreboard Key:<br />
"<b><code>_</code></b>" Waiting for Connection, 
"<b><code>S</code></b>" Starting up, 
"<b><code>R</code></b>" Reading Request,<br />
"<b><code>W</code></b>" Sending Reply, 
"<b><code>K</code></b>" Keepalive (read)
```


### Reference

* [How to Monitor Apache Performance Using mod_status in Ubuntu](https://www.tecmint.com/ubuntu-apache-mod_status/)