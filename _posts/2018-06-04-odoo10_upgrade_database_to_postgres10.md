---
layout: post
title:  "Odoo10 Upgrade the PostgreSQL from 9.5 to 10"
date:   2018-06-04 09:18:01 +0700
categories: [odoo, postgresql]
published: true
---

### Backup Database
* If you are use the LXD. You can snapshot a container with:
```
lxc snapshot <container>
```
Replace the `<container>` with your container name. For more about **LXD container** read
[LXD 2.0: Your first LXD container [3/12]](https://blog.ubuntu.com/2016/03/22/lxd-2-0-your-first-lxd-container)

* Backup a PostgreSQL database by `pg_dump()` .
The [`pg_dump`](https://www.postgresql.org/docs/current/static/app-pgdump.html) is a utility for backing up a PostgreSQL database.
The `/opt/odoo10_backup/` is backup folder.
```
pg_dump -Ft odoo_database > /opt/odoo10_backup/odoo_database.tar
```

### Backup Odoo local files
Odoo local files default is on `odoo/.local/share/Odoo`.
If your Odoo install folder is `/opt`. Then use **rsync** backup to `/opt/odoo10_backup/` folder
```
rsync -azP --delete /opt/odoo /opt/odoo10_backup/
```
If you are use the LXD. You aren't take this step due to snapshot a container backup
all system data.

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

### Check your upgrade
* Check PostreSQL 10 with `pg_lsclusters`:
```
# pg_lsclusters
Ver Cluster Port Status Owner    Data directory              Log file
10  main    5432 online postgres /var/lib/postgresql/10/main /var/log/postgresql/postgresql-10-main.log
```

* Check Odoo service is online:
```
# service odoo status
● odoo.service - LSB: Start odoo daemon at boot time
   Loaded: loaded (/etc/init.d/odoo; bad; vendor preset: enabled)
   Active: active (running) since Tue 2018-06-05 07:03:01 UTC; 1min 8s ago
```

* Try to Login


### Fixed Odoo Programming Error
Sometime after upgrade you found some Error. This some solution to fixe it.
* **There is no primary key for referenced table `res_users`.**
```
ALTER TABLE res_users ADD PRIMARY KEY (id);
```
I found the solution in
[In odoo where advanced-view-editor in saving files](https://stackoverflow.com/questions/36850440/in-odoo-where-advanced-view-editor-in-saving-files).
