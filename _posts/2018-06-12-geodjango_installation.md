---
layout: post
title:  "GeoDjango Installation with Virtualenv"
date:   2018-06-11 14:08:01 +0700
categories: gis
published: false
---
Details for each of the requirements and installation instructions are provided in [GeoDjango Installation](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/install/)

### Python and Django
* Install pip.
```
$ apt install python-pip
$ pip3 -V
pip 9.0.1 from /usr/lib/python3/dist-packages (python 3.6)
```

* Install Virtualenv
```
$ [sudo] pip3 install virtualenv
```
[`Virtualenv`](https://virtualenv.pypa.io/en/stable/) is a tool to create isolated Python environments.

* Create a new environment
```
$ virtualenv /opt/django
Using base prefix '/usr'
New python executable in /opt/django/bin/python3
Also creating executable in /opt/django/bin/python
Installing setuptools, pip, wheel...done.
```
Where `/opt/django` is a directory to place the new virtual environment.

* Activate script
```
$ cd /opt/django/
$ source bin/activate
(django)$
```
The `activate` script will also modify your shell prompt to indicate which environment is currently active.

* Install GeoDjango
```
(django)$ pip install Django
Collecting Django
....
Installing collected packages: pytz, Django
Successfully installed Django-2.0.6 pytz-2018.4
(django)$ django-admin --version
2.0.6
(django)$
```

### Installing Geospatial Libraries
On Debian/Ubuntu, you are advised to install the following packages
which will install, directly or by dependency, the required geospatial libraries:
```
$ [sudo] apt-get install binutils libproj-dev gdal-bin
```

#### GEOS API
[GEOS](https://trac.osgeo.org/geos/) stands for Geometry Engine - Open Source,
and is a C++ library, ported from the Java Topology Suite.
```
$ [sudo] apt-get install libgeos++
```

#### PROJ.4
[PROJ](https://proj4.org/index.html) is a generic coordinate transformation software,
that transforms geospatial coordinates from one coordinate reference system (CRS) to another.
On Ubuntu the APT package manager is used:
```
$ [sudo] apt-get install proj-bin
```

#### GDAL API
[GDAL](http://www.gdal.org/) stands for Geospatial Data Abstraction Library, and is a veritable “Swiss army knife” of GIS data functionality.
```
$ [sudo] apt install gdal-bin
Reading package lists... Done
Building dependency tree
...
Unpacking gdal-bin (2.2.3+dfsg-2) ...
Processing triggers for man-db (2.8.3-2) ...
Setting up gdal-bin (2.2.3+dfsg-2) ...
```

### Creating a Project
```
(django)$ django-admin startproject kapany
(django)$ tree
.
├── kapany
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── manage.py

1 directory, 5 files
(django)$ cd kapany/
```
This will create a directory called `kapany` within your current directory.

#### Install pyscopg2
```
(django)$ pip install psycopg2
```

#### DATABASES Configuration
Set the ENGINE setting to one of the spatial backends. Edit `settings.py` in the
`kapany` folder.

```
# Database
# https://docs.djangoproject.com/en/2.0/ref/settings/#databases

DATABASES = {
    'default': {        
        'ENGINE': 'django.contrib.gis.db.backends.postgis',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

#### migrate
```
python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying auth.0009_alter_user_last_name_max_length... OK
  Applying sessions.0001_initial... OK
(django) root@kapany:/opt/django/kapany#
```

#### createsuperuser
```
(django) root@kapany:/opt/django/kapany# python manage.py createsuperuser
Username (leave blank to use 'root'):
Email address: p.kitcharoen@interlinktelecom.co.th
Password:
Password (again):
Superuser created successfully.
(django) root@kapany:/opt/django/kapany#
````
#### ALLOWED_HOSTS
setting `ALLOWED_HOSTS` in `settings.py` by append :
For further reading [read from here].
```
ALLOWED_HOSTS = ['your_ip_address', 'localhost', '127.0.0.1']
```

### runserver
```
(django) root@kapany:/opt/django/kapany# python manage.py runserver 0.0.0.0:8000
Performing system checks...

System check identified no issues (0 silenced).
June 12, 2018 - 09:51:16
Django version 2.0.6, using settings 'kapany.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```
