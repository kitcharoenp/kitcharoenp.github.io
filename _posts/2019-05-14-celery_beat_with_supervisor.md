---
layout: post
title: "Celery and Celery Beat Daemonization with Supervisor"
published: true
categories: [celery, supervisor]
---

> **[Supervisor](http://supervisord.org/index.html)** is a client/server system that allows its users to monitor and control a number of processes on UNIX-like operating systems.

First of all, you’ll need to have [Celery and Celery Beat running with Django](https://docs.celeryproject.org/en/latest/django/first-steps-with-django.html).

### Installing Supervisor
Following [**Supervisor** Installation Instructions](http://supervisord.org/installing.html) depend on your system.

**directory structure:**

```shell
# Installing With Pip
# Cackup
$ cd /etc/
$ sudo mkdir supervisor
$ cd /etc/supervisor
$ sudo mkdir conf.d

$ tree /etc/supervisor -L 1
/etc/supervisor
├── conf.d
```

```shell
# Installing a Distribution Package
$ tree /etc/supervisor -L 1
/etc/supervisor
├── conf.d
├── supervisord.conf

# Cackup
$ cd /etc/supervisor
$ sudo cp supervisord.conf supervisord.conf.default.factory
```

### Edit ***Supervisor*** configuration file
```shell
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

### Create celery worker supervisor configuration
1. Get configuration from the [Official Celery](https://github.com/celery/celery/blob/master/extra/supervisord/celeryd.conf).
```shell
$ cd /etc/supervisor/conf.d/
$ sudo wget https://github.com/celery/celery/blob/master/extra/supervisord/celeryd.conf
$ ls
celeryd.conf
```
2. Edit `command`, `directory` and `user` according to your project.

    **Example My Project**
    * project name : `kapany_proj`
    * project directory : `/opt/django_projects/kapany_proj`
    * project virtualenv : `/opt/django_projects/kapany_proj/kapany_env/`
    * celery user : `celery`

    ```
    ...
    command=/opt/django_projects/kapany_proj/kapany_env/bin/celery -A kapany_proj worker --loglevel=info
    directory=/opt/django_projects/kapany_proj
    user=celery
    ...
    ```

### Create celery beat supervisor configuration
1. Get configuration from the [Official Celery](https://github.com/celery/celery/blob/master/extra/supervisord/celerybeat.conf).
```shell
$ cd /etc/supervisor/conf.d/
$ sudo wget https://github.com/celery/celery/blob/master/extra/supervisord/celerybeat.conf
$ ls
celeryd.conf  celerybeat.conf
```
2. Edit `command`, `directory` and `user` according to your project.

    **Example My Project**
    * project name : `kapany_proj`
    * project directory : `/opt/django_projects/kapany_proj`
    * project virtualenv : `/opt/django_projects/kapany_proj/kapany_env/`
    * celery user : `celery`
    * beat scheduler : `django_celery_beat.schedulers:DatabaseScheduler`
    * pidfile : `/tmp/celerybeat.pid`


    ```
    ...
    command=/opt/django_projects/kapany_proj/kapany_env/bin/celery -A kapany_proj beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler --pidfile="/tmp/celerybeat.pid"
    directory=/opt/django_projects/kapany_proj
    user=celery
    ...
    ```

### [Running supervisord](http://supervisord.org/running.html#running-supervisord)
> To start supervisord, run `$BINDIR/supervisord`. The resulting process will daemonize itself and detach from the terminal.


### Reread and update a supervisor configuration
* **[reread](http://supervisord.org/running.html)** : Reload the daemon’s configuration files, without add/remove (no restarts)
* **[update](http://supervisord.org/running.html)** : Reload config and add/remove as necessary, and will restart affected programs

```shell
$ sudo supervisorctl reread
$ sudo supervisorctl update
```

### Get all process status info
```
$ sudo supervisorctl status
celerybeat           RUNNING   pid 5897, uptime 1:16:42
celery               RUNNING   pid 5898, uptime 1:16:40
```

### View Log File

* `/var/log/supervisor/supervisord.log`
* `/var/log/celery/beat.log`
