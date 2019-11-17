---
layout: post
title:  "Set up PostgreSQL and PostGIS extension on Ubuntu"
date:   2018-05-28 16:38:01 +0700
categories: [postgresql, postgis]
published: true
---
Get more on [UsersWikiPostGIS2](https://trac.osgeo.org/postgis/wiki/UsersWikiPostGIS24UbuntuPGSQL10Apt)
for describes how to install Postgresql 10, PostGIS 2.4, pgRouting 2.6, PGAdmin on Ubuntu.

You can use the [UbuntuGIS](https://wiki.ubuntu.com/UbuntuGIS). UbuntuGIS is a repository
that can be added in your sources.list which will provide up to date GIS packages.
After adding the UbuntuGIS repository corresponding to their distribution (in sources.list),
you can easily install on your machine each of the GIS applications.

### Verify what version of ubuntu
```
$ sudo lsb_release -a
sudo lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 18.04 LTS
Release:	18.04
Codename:	bionic
```
### Add respository to sources.list
Replace `ubuntu_codename` with **Codename** from `lsb_release` command.
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt ubuntu_codename-pgdg main" >> /etc/apt/sources.list'
```

In this case is `bionic` (Ubuntu 18.04 LTS).
```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt bionic-pgdg main" >> /etc/apt/sources.list'
```

### Import the gpg keys and update packages
`apt-key` is used to manage the list of keys used by apt to authenticate packages.
```
wget --quiet -O - http://apt.postgresql.org/pub/repos/apt/ACCC4CF8.asc | sudo apt-key add -

sudo apt update
```

### Install postgresql and postgis
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

### Install pgRouting
[pgRouting](https://pgrouting.org/) extends the PostGIS / PostgreSQL geospatial database to provide geospatial routing functionality.
```
sudo apt install postgresql-10-pgrouting
```

### Create a database and schema
The following may be executed from the SQL Shell as the postgres user:
* Create a database name is **gisdb** with command `CREATE DATABASE`
* Create a schema name is **postgis** with command `CREATE SCHEMA`.
[CREATE SCHEMA](https://www.postgresql.org/docs/10/static/sql-createschema.html) enters a new schema into the current database. A schema is essentially a namespace: it contains named objects (tables, data types, functions, and operators) whose names can duplicate those of other objects existing in other schemas.

```
postgres=# CREATE DATABASE gisdb;

postgres=#\l
                                       List of databases
       Name        |    Owner    | Encoding |   Collate   |    Ctype    |   Access privileges   
-------------------+-------------+----------+-------------+-------------+-----------------------
 finance           | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 gisdb             | postgres    | UTF8     | en_US.UTF-8 | en_US.UTF-8 |

postgres=#\connect gisdb;

gisdb=# CREATE SCHEMA postgis;
ALTER DATABASE gisdb SET search_path=public, postgis, contrib;
```

### Enable the postgis extension
```
gisdb=# CREATE EXTENSION postgis SCHEMA postgis;
```

### Show postgis version
Reconnect the `gisdb` database. Use `postgis_full_version()` to reports full postgis version and build configuration infos.
```
gisdb=#SELECT postgis_full_version();
-[ RECORD 1 ]--------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
postgis_full_version | POSTGIS="2.4.3 r16312" PGSQL="100" GEOS="3.6.2-CAPI-1.10.2 4d2925d6" PROJ="Rel. 4.9.3, 15 August 2016" GDAL="GDAL 2.2.3, released 2017/11/20" LIBXML="2.9.4" LIBJSON="0.12.1" LIBPROTOBUF="1.2.1" RASTER
gisdb=#
```

### Managing the database
Create a new postgres user (`ubuntu`) which we will use to connect. Replace `secure_password`
with your password which need to use login database. Chnage owner of the `gisdb` database to
`ubuntu` user and upgrade to be a superuser.

Reference to [Creating a spatial database](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/install/postgis/) for GeoDjango Installation
> The database user must be a superuser in order to run `CREATE EXTENSION postgis;`.The command is run during the migrate process.

```
# su postgres
$ psql
psql (10.3 (Ubuntu 10.3-1))
Type "help" for help.

postgres=# CREATE USER ubuntu WITH PASSWORD secure_password;
postgres=# ALTER DATABASE gisdb OWNER TO ubuntu;
postgres=# ALTER USER ubuntu WITH SUPERUSER;
postgres=# \du
List of roles
Role name |                         Attributes                         | Member of
-----------+------------------------------------------------------------+-----------
postgres  | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
ubuntu    | Superuser
postgres=# \q
```
