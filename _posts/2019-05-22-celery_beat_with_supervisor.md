---
layout: post
title: "Celery and Celery Beat Daemonization with Supervisor"
published: true
categories: [celery, supervisor]
---

> **[Supervisor](http://supervisord.org/index.html)** is a client/server system that allows its users to monitor and control a number of processes on UNIX-like operating systems.

[Installing and Configuring Supervisor on Ubuntu 16.04](https://www.vultr.com/docs/installing-and-configuring-supervisor-on-ubuntu-16-04)

http://www.pyrunner.com/weblog/2015/10/31/master-background-tasks-celery-rabbitmq-and-supervisor/

First of all, you’ll need to have [Celery and Celery Beat running with Django](https://docs.celeryproject.org/en/latest/django/first-steps-with-django.html).

### Installing Supervisor
Following [**Supervisor** Installation Instructions](http://supervisord.org/installing.html) depend on your system.

**directory structure:**
```shell
$ tree /etc/supervisor -L 1
/etc/supervisor
├── conf.d
├── supervisord.conf
```

### Edit ***Supervisor*** configuration file
```shell
# backup
$ cd /etc/supervisor
$ sudo cp supervisord.conf supervisord.conf.default.factory

# update with new config
$ echo_supervisord_conf | sudo tee supervisord.conf
```
Keep all configuration as default. Edit the `[include]` section from
```shell
;[include]
;files = relative/directory/*.ini
```
to
```shell
[include]
files = /etc/supervisor/conf.d/*.conf
```

### Settings Celery

### Settings Celery Beat

###

### Remove the default Supervisor init script
```shell
$ sudo update-rc.d -f supervisor remove
$ sudo rm -f /etc/init.d/supervisor
$ sudo rm -f /etc/default/supervisor
```
