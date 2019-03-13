---
layout: post
title: "[Celery] beat as daemon"
published: true
categories: [celery]
---

> **[django_celery_beat](https://pypi.org/project/django_celery_beat/)** is extension enables you to store the periodic task schedule in the database, and presents a convenient admin interface to manage periodic tasks at runtime.

### Install Extension
see [using custom scheduler classes](http://docs.celeryproject.org/en/latest/userguide/periodic-tasks.html#using-custom-scheduler-classes) for more information.
*  Use pip to install the package:

    ```shell
    (kapany_env)$ pip3 install django-celery-beat
    ```

*  Config **settings.py**:

    ```python
    # Path to change directory to at start.
    CELERYD_CHDIR="/opt/django_projects/kapany_proj/"
    # django_celery_beat
    INSTALLED_APPS += ['django_celery_beat']
    ```

*  Apply Django database migrations:

    ```shell
    (kapany_env)$ python manage.py migrate
    Running migrations:
    Applying django_celery_beat.0001_initial... OK
    Applying django_celery_beat.0002_auto_20161118_0346... OK
    Applying django_celery_beat.0003_auto_20161209_0049... OK
    Applying django_celery_beat.0004_auto_20170221_0000... OK
    Applying django_celery_beat.0005_add_solarschedule_events_choices_squashed_0009_merge_20181012_1416... OK
    Applying django_celery_beat.0006_periodictask_priority... OK
    (kapany_env)$
    ```

*  Start the celery beat service manual
```shell
(kapany_env)$ celery -A kapany_proj beat -l info --scheduler django_celery_beat.schedulers:DatabaseScheduler
celery beat v4.2.1 (windowlicker) is starting.
__    -    ... __   -        _
LocalTime -> 2019-03-13 09:55:20
Configuration ->
    . broker -> amqp://guest:**@localhost:5672//
    . loader -> celery.loaders.app.AppLoader
    . scheduler -> django_celery_beat.schedulers.DatabaseScheduler

    . logfile -> [stderr]@%INFO
    . maxinterval -> 5.00 seconds (5s)
[2019-03-13 09:55:20,161: INFO/MainProcess] beat: Starting...
[2019-03-13 09:55:20,162: INFO/MainProcess] Writing entries...
[2019-03-13 09:55:25,320: INFO/MainProcess] Writing entries...
```

*  Visit the Django-Admin interface to set up some periodic tasks.           

### Create a configuration file
1. Create the empty `/etc/default/celerybeat` configuration file for the init script
    ```shell
    $ sudo touch /etc/default/celerybeat
    $ sudo vim /etc/default/celerybeat
    ```

2. Copy  [example configuration](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#generic-initd-celerybeat-example)
    and paste to `/etc/default/celerybeat`

3. Edit the [configuration option](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#generic-initd-celerybeat-options) according to your project.

    * project name : `kapany_proj`
    * project directory : `/opt/django_projects/kapany_proj/`
    * project env : `/opt/django_projects/kapany_proj/kapany_env/`

    ```shell
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

    # Extra arguments to celerybeat
    # Database-backed Periodic Tasks `django_celery_beat`
    CELERYBEAT_OPTS="--schedule=django_celery_beat.schedulers:DatabaseScheduler"

    ```

### Create the init script
Create the init script in `/etc/init.d/celerybeat`. See the [extra/generic-init.d/](https://github.com/celery/celery/tree/3.1/extra/generic-init.d/) directory Celery distribution.

```shell
# download
$ wget https://github.com/celery/celery/blob/3.1/extra/generic-init.d/celerybeat
# executable
$ chmod +x celerybeat
# move
$ sudo mv celerybeat /etc/init.d/
```


### [Verbose the init-scripts](http://docs.celeryproject.org/en/latest/userguide/daemonizing.html#troubleshooting)

```shell
$ sudo sh -x /etc/init.d/celerybeat start
```
**Output example**
```

```

### Enable the daemon
```shell
$ sudo update-rc.d celerybeat defaults
$ sudo service celerybeat start
```
