---
layout: post
title:  "Set up PostgreSQL, PostGIS and Django on Ubuntu"
date:   2018-05-28 16:38:01 +0700
categories: [postgresql, postgis, django]
published: false
---
[UsersWikiPostGIS2](https://trac.osgeo.org/postgis/wiki/UsersWikiPostGIS24UbuntuPGSQL10Apt)
### Verify What version of Ubuntu
```
$ sudo lsb_release -a
sudo lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04 LTS
Release:	18.04
Codename:	bionic
```
### Add Respository to sources.list
Replace `ubuntu_codename` with **Codename** from `lsb_release` command.
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt ubuntu_codename-pgdg main" >> /etc/apt/sources.list'
```

In this case is 'bionic' (Ubuntu 18.04 LTS).
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt bionic-pgdg main" >> /etc/apt/sources.list'
```

### Add Keys and Update Packages
```
wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -
sudo apt update
```

### Install PostgreSQL and PostGIS
The following will install PostgreSQL 10, PostGIS 2.4

```
sudo apt install postgresql-10
sudo apt install postgresql-10-postgis-2.4
sudo apt install postgresql-10-postgis-scripts
...
# pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
10  main    5432 online postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
#
```

### Install shp2pgsql, raster2pgsql
To get the commandline tools shp2pgsql, raster2pgsql you need to do this
```
sudo apt install postgis
```

### Enable the PostGIS extension
Create a database then enable the PostGIS extension
```
postgres=# create database gisdb;
CREATE DATABASE
\c gisdb;

CREATE SCHEMA postgis;
ALTER DATABASE gisdb SET search_path=public, postgis, contrib;

### Enable the PostGIS extension
```
postgres=# \c gisdb;
postgres=# CREATE EXTENSION postgis SCHEMA postgis;
```

### Show PostGIS version
disconnect and connect postgres
```
postgres=#\c gisdb;
postgres=#SELECT postgis_full_version();
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
