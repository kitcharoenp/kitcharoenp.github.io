---
layout: post
title:  "Odoo10 Upgrade the PostgreSQL from 9.5 to 10"
date:   2018-06-04 09:18:01 +0700
categories: [odoo, postgresql]
published: false
---

### Backup database and Odoo local file
* Take a snapshot of container, if you use the lxc (Linux container).
* Use `pg_dump()`, if you aren't user lxc.

### Install PostgreSQL 10
* Import the GPG key for PostgreSQL packages. Then add the repository to your system.
```
$ wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
$ sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

* Install PostgreSQL Database Server
```
$ sudo apt-get update
$ sudo apt-get install postgresql-10 postgresql-contrib
$ sudo pg_lsclusters
Ver Cluster Port Status Owner    Data directory               Log file
9.5 main    5432 online postgres /var/lib/postgresql/9.5/main /var/log/postgresql/postgresql-9.5-main.log
10  main    5433 online postgres /var/lib/postgresql/10/main  /var/log/postgresql/postgresql-10-main.log
```

### Upgrading Data via pg_upgradecluster
I get this step from [How do i upgrade my Postgresql 9.5 to Postgresql 10 on ubuntu 16.04
](https://stackoverflow.com/questions/47029055/how-do-i-upgrade-my-postgresql-9-5-to-postgresql-10-on-ubuntu-16-04?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa)

* Stop the PostreSQL service
```
service postgresql stop
```

* Drop the PostreSQL 10 main cluster
```
pg_dropcluster --stop 10 main
```

* Upgrade an existing PostgreSQL cluster to a new major version by [pg_upgradecluster](http://manpages.ubuntu.com/manpages/trusty/man8/pg_upgradecluster.8.html)
```
pg_upgradecluster -m upgrade 9.5 main
```

* Stop the PostreSQL 9.5 main cluster:
```
pg_dropcluster 9.5 main --stop
```

* Remove the PostreSQL 9.5 packages:
```
apt-get autoremove --purge postgresql-9.5
```

* Restart PostgreSQL:
```
service postgresql start
```

### Fixed Programming Error due to Database Upgrade
There is no primary key for referenced table `res_users`.
Solution : create a new database with demo data then install some module.

I found the solution in
[In odoo where advanced-view-editor in saving files](https://stackoverflow.com/questions/36850440/in-odoo-where-advanced-view-editor-in-saving-files)
* get the list of primary keys
```
update ir_module_module set state = 'installed' where state = 'to upgrade';
```
