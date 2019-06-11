---
layout: post
title: "Install Celery on Django"
published: true
categories: [celery]
---

### Installing Broker
RabbitMQ is feature-complete, stable, durable and easy to install. It’s an excellent choice for a production environment. see [Using RabbitMQ](http://docs.celeryproject.org/en/latest/getting-started/brokers/rabbitmq.html#using-rabbitmq)

#### Install Latest Erlang
*  [How to install Latest Erlang on Ubuntu 18.04 LTS](https://computingforgeeks.com/how-to-install-latest-erlang-on-ubuntu-18-04-lts/)

### Install Latest RabbitMQ
*  [How to install Latest RabbitMQ Server on Ubuntu 18.04 LTS](https://computingforgeeks.com/how-to-install-latest-rabbitmq-server-on-ubuntu-18-04-lts/)


### Enable the RabbitMQ Management Dashboard (Optional)
```shell
$ sudo rabbitmq-plugins enable rabbitmq_management

# The Web service should be listening on TCP port 15672
$ sudo ss -tunelp | grep 15672
tcp    LISTEN   0        128                                    0.0.0.0:15672                                        0.0.0.0:*                                   users:(("beam.smp",pid=4751,fd=77)) uid:141 ino:213934 sk:23 <->  
```
Access it by opening the URL `http://[server IP|Hostname]:15672`

### Installing Celery
```shell
(your_env)$ pip install celery
```

### [Using Celery with Django](http://docs.celeryproject.org/en/latest/django/first-steps-with-django.html#first-steps-with-django)
To use Celery with your Django project you must first define an instance of the Celery library (called an “app”)

My Django project layout:
```shell
$ tree your_proj/ -L 1
kapany_proj/
├── your_env
├── your_proj
├── manage.py
└── static
```

Create a new `your_proj/your_proj/celery.py` module that defines the Celery instance:
```python
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery

# set the default Django settings module for the 'celery' program.
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'your_proj.settings')

app = Celery('your_proj')

# Using a string here means the worker doesn't have to serialize
# the configuration object to child processes.
# - namespace='CELERY' means all celery-related configuration keys
#   should have a `CELERY_` prefix.
app.config_from_object('django.conf:settings', namespace='CELERY')

# Load task modules from all registered Django app configs.
app.autodiscover_tasks()


@app.task(bind=True)
def debug_task(self):
    print('Request: {0!r}'.format(self.request))
```

import this app in your `your_proj/your_proj/__init__.py` module.
```python
from __future__ import absolute_import, unicode_literals

# This will make sure the app is always imported when
# Django starts so that shared_task will use this app.
from .celery import app as celery_app

__all__ = ('celery_app',)
```

### Configuration
```python
# Celery settings
# TODO : set `admin` and strong password in rabbitmq server
CELERY_BROKER_URL = 'amqp://guest:guest@localhost//'
```
