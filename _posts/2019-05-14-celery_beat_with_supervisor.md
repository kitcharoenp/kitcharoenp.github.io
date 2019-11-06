---
layout: post
title: "Supervisor + Celery Beat Daemonization"
published: true
categories: [celery]
---

> **[Supervisor](http://supervisord.org/index.html)** is a client/server system that allows its users to monitor and control a number of processes on UNIX-like operating systems.

First of all, you’ll need to have [Celery and Celery Beat running with Django](https://docs.celeryproject.org/en/latest/django/first-steps-with-django.html).

### Installing Supervisor
Following [**Supervisor** Installation Instructions](http://supervisord.org/installing.html) depend on your system.

**directory structure:**

```shell
# Installing With Pip
$ cd /etc/
$ sudo mkdir supervisor
$ cd /etc/supervisor
$ sudo mkdir conf.d

$ tree /etc/supervisor -L 1
/etc/supervisor
├── conf.d
```

```shell
# Installing With Distribution Package
$ tree /etc/supervisor -L 1
/etc/supervisor
├── conf.d
├── supervisord.conf

# Backup
$ cd /etc/supervisor
$ sudo cp supervisord.conf supervisord.conf.default.factory
```

### Create / Update ***Supervisor*** configuration file
```shell
#   create / update configuration file
$ echo_supervisord_conf | sudo tee /etc/supervisor/supervisord.conf
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
1. Get configuration from the [Official Celery](https://raw.githubusercontent.com/celery/celery/master/extra/supervisord/celeryd.conf).
```shell
$ cd /etc/supervisor/conf.d/
$ sudo wget https://raw.githubusercontent.com/celery/celery/master/extra/supervisord/celeryd.conf
$ ls
celeryd.conf
```
2. Edit `command`, `directory` and `user` according to your project.

    **Example My Project**
    * project name : `_proj`
    * project directory : `/home/ubuntu/_proj`
    * project virtualenv : `/home/ubuntu/_proj/virt_env/`
    * project user : `ubuntu`

    ```
    ...
    command=/home/ubuntu/_proj/virt_env/bin/celery -A _proj worker --loglevel=info
    directory=/home/ubuntu/_proj
    user=ubuntu
    ...
    ```

### Create celery beat supervisor configuration
1. Get configuration from the [Official Celery](https://raw.githubusercontent.com/celery/celery/master/extra/supervisord/celerybeat.conf).
```shell
$ cd /etc/supervisor/conf.d/
$ sudo wget https://raw.githubusercontent.com/celery/celery/master/extra/supervisord/celerybeat.conf
$ ls
celeryd.conf  celerybeat.conf
```
2. Edit `command`, `directory` and `user` according to your project.

    **Example My Project**
    * project name : `_proj`
    * project directory : `/home/ubuntu/_proj`
    * project virtualenv : `/home/ubuntu/_proj/virt_env/`
    * project user : `ubuntu`
    * beat scheduler : `django_celery_beat.schedulers:DatabaseScheduler`
    * pidfile : `/tmp/celerybeat.pid`


    ```
    ...
    command=/home/ubuntu/_proj/virt_env/bin/celery -A _proj beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler --pidfile="/tmp/celerybeat.pid"
    directory=/home/ubuntu/_proj
    user=ubuntu
    ...
    ```

### [Running supervisord](http://supervisord.org/running.html#running-supervisord)
> To start supervisord, run `$BINDIR/supervisord`. The resulting process will daemonize itself and detach from the terminal.

```shell
# create a celery log directory if it is not exist
$ sudo mkdir /var/log/celery
$ sudo supervisord
...
a "-c" argument specifying an absolute path to a configuration file for improved security.
'Supervisord is running as root and it is searching '
$ sudo supervisorctl status
celery                           RUNNING   pid 21286, uptime 0:03:40
celerybeat                       RUNNING   pid 21285, uptime 0:03:40


```


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

* `tail -f -n 200 /tmp/supervisord.log`

```shell
2019-05-28 08:36:21,954 INFO Included extra file "/etc/supervisor/conf.d/celerybeat.conf" during parsing
2019-05-28 08:36:21,954 INFO Included extra file "/etc/supervisor/conf.d/celeryd.conf" during parsing
2019-05-28 08:36:21,973 INFO RPC interface 'supervisor' initialized
2019-05-28 08:36:21,973 CRIT Server 'unix_http_server' running without any HTTP authentication checking
2019-05-28 08:36:21,975 INFO daemonizing the supervisord process
2019-05-28 08:36:21,976 INFO supervisord started with pid 21280
2019-05-28 08:36:22,980 INFO spawned: 'celerybeat' with pid 21285
2019-05-28 08:36:22,983 INFO spawned: 'celery' with pid 21286
2019-05-28 08:36:33,080 INFO success: celerybeat entered RUNNING state, process has stayed up for > than 10 seconds (startsecs)
2019-05-28 08:36:33,081 INFO success: celery entered RUNNING state, process has stayed up for > than 10 seconds (startsecs)
```

* `tail -f -n 200 /var/log/celery/beat.log`

```shell
[2019-05-28 15:36:30,139: INFO/MainProcess] beat: Starting...
[2019-05-28 15:36:30,139: INFO/MainProcess] Writing entries...
[2019-05-28 15:36:35,858: INFO/MainProcess] Writing entries...
[2019-05-28 15:39:36,515: INFO/MainProcess] Writing entries...
[2019-05-28 15:42:37,169: INFO/MainProcess] Writing entries...
[2019-05-28 15:45:37,819: INFO/MainProcess] Writing entries...
```
