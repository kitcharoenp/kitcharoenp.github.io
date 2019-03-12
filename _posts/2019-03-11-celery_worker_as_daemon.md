---
layout: post
title: "[Celery] worker as daemon"
published: true
categories: [celery]
---
You probably want to use a daemonization tool to start the worker in the background.
Running the worker in the background as a daemon see [Daemonization](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#daemonization) for more information.

### Create a configuration file
1. Create the empty `/etc/default/celeryd` configuration file for the init script
    ```shell
    $ sudo touch /etc/default/celeryd
    $ sudo vim /etc/default/celeryd
    ```

2. Copy  [example configuration](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#init-script-celeryd)
    and paste to `/etc/default/celeryd`

3. Edit the [configuration option](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#available-options) according to your project.

    * project name : `kapany_proj`
    * project directory : `/opt/django_projects/kapany_proj/`
    * project env : `/opt/django_projects/kapany_proj/kapany_env/`

    ```shell
    # Names of nodes to start
    #   most people will only start one node:
    CELERYD_NODES="worker1"
    #   but you can also start multiple and configure settings
    #   for each in CELERYD_OPTS
    #CELERYD_NODES="worker1 worker2 worker3"
    #   alternatively, you can specify the number of nodes to start:
    #CELERYD_NODES=10

    # Absolute or relative path to the 'celery' command:
    #CELERY_BIN="/usr/local/bin/celery"
    #CELERY_BIN="/virtualenvs/def/bin/celery"
    CELERY_BIN="/opt/django_projects/kapany_proj/kapany_env/bin/celery"

    # App instance to use
    # comment out this line if you don't use an app
    CELERY_APP="kapany_proj"
    # or fully qualified:
    #CELERY_APP="proj.tasks:app"

    # Where to chdir at start.
    CELERYD_CHDIR="/opt/django_projects/kapany_proj/"

    # Extra command-line arguments to the worker
    CELERYD_OPTS="--time-limit=300 --concurrency=8"
    # Configure node-specific settings by appending node name to arguments:
    #CELERYD_OPTS="--time-limit=300 -c 8 -c:worker2 4 -c:worker3 2 -Ofair:worker1"

    # Set logging level to DEBUG
    #CELERYD_LOG_LEVEL="DEBUG"

    # %n will be replaced with the first part of the nodename.
    CELERYD_LOG_FILE="/var/log/celery/%n%I.log"
    CELERYD_PID_FILE="/var/run/celery/%n.pid"

    # Workers should run as an unprivileged user.
    #   You need to create this user manually (or you can choose
    #   a user/group combination that already exists (e.g., nobody).
    CELERYD_USER="celery"
    CELERYD_GROUP="celery"

    # If enabled pid and log directories will be created if missing,
    # and owned by the userid/group configured.
    CELERY_CREATE_DIRS=1

    ```

### Create celery user
Use the `adduser` command to add a new user to your system. Follow the prompts to set the new user's information.
```
$ sudo adduser celery
```

### Create the init script
Create the init script in `/etc/init.d/celeryd`. See the [extra/generic-init.d/](https://github.com/celery/celery/tree/3.1/extra/generic-init.d/) directory Celery distribution.

```shell
# download
$ wget https://github.com/celery/celery/blob/3.1/extra/generic-init.d/celeryd
# executable
$ chmod +x celeryd
# move
$ sudo mv celeryd /etc/init.d/
```

### Create logs directory

```shell
# make new dir
$ sudo mkdir /var/log/celery

# change owner
$ sudo chown -R celery: /var/log/celery
```

### [Verbose the init-scripts](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#troubleshooting)

```shell
$ sudo sh -x /etc/init.d/celeryd start
```
**Output example**
```
$ sudo sh -x /etc/init.d/celeryd start
[sudo] password for your_username:
+ VERSION=10.1
+ echo celery init v10.1.
celery init v10.1.
+ id -u
+ [ 0 -ne 0 ]
+ origin_is_runlevel_dir
...
- Changing group of '/var/run/celery' to 'celery'
+ chgrp celery /var/run/celery
+ maybe_die Couldn't change group of /var/run/celery
+ [ 0 -ne 0 ]
+ start_workers
+ [ ! -z  ]
+ _chuid start worker1 --workdir=/opt/django_projects/kapany_proj/ --pidfile=/var/run/celery/%n.pid --logfile=/var/log/celery/%n%I.log --loglevel=INFO --app=kapany_proj --time-limit=300 --concurrency=8
+ su celery -c /opt/django_projects/kapany_proj/kapany_env/bin/celery multi start worker1 --workdir=/opt/django_projects/kapany_proj/ --pidfile=/var/run/celery/%n.pid --logfile=/var/log/celery/%n%I.log --loglevel=INFO --app=kapany_proj --time-limit=300 --concurrency=8
celery multi v4.2.1 (windowlicker)
> Starting nodes...
	> worker1@vhcalnplci: OK
+ exit 0
```

### Enable the daemon
```shell
$ sudo update-rc.d celeryd defaults
$ sudo service celeryd start
```
