---
layout: post
title: "Automatically Running Supervisord on Startup"
published: false
categories: [supervisor]
---

### Remove the default Supervisor init script
```shell
$ sudo update-rc.d -f supervisor remove
$ sudo rm -f /etc/init.d/supervisor
$ sudo rm -f /etc/default/supervisor
```
