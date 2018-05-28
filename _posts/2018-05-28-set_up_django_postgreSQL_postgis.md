---
layout: post
title:  "Set up Django, PostgreSQL and PostGIS on Ubuntu"
date:   2018-05-28 16:38:01 +0700
categories: [django, postgresql, postgis]
---
Read [How To Install Django and Set Up a Development Environment on Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-install-django-and-set-up-a-development-environment-on-ubuntu-16-04) for more detail.
### Pyhon 3
Install pip, the Python package manager.

```
# apt-get install python3-pip python3-dev -y
```

### Python Virtualenv
The `virtualenv` is a tool to create isolated Python environments.

```
# pip3 install virtualenv
```

### PostgreSQL Database
Install the default Postgres repositories.

```
# apt-get install postgresql postgresql-contrib -y
...
# pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
10  main    5432 online postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
#
```

### PostgreSQL Database User
Create a PostgreSQL database user (`ubuntu`) which we will use to connect. Replace `secure_password`
with your password which need to use login database.

```
# su postgres
$ psql
could not change directory to "/root": Permission denied
psql (10.3 (Ubuntu 10.3-1))
Type "help" for help.

postgres=# CREATE USER ubuntu WITH PASSWORD secure_password
postgres=# \q
```
