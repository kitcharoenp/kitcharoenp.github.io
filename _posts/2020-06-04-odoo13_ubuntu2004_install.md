---
layout: post
title: "Install Odoo13 on Ubuntu 20.04"
published: true
categories: [odoo]
---

### Create a new container
```
lxc launch ubuntu:20.04 odoo13
```

### Configuring a Static IP Address
Edit
```
# access container
lxc exec odoo13 bash

# edit the netplan configuration file
vim /etc/netplan/50-cloud-init.yaml
```

View
```
cat /etc/netplan/50-cloud-init.yaml
# This file is generated from information provided by the datasource.  Changes
# to it will not persist across an instance reboot.  To disable cloud-init's
# network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
    version: 2
    ethernets:
        eth0:
            addresses: [10.10.10.219/24]
            gateway4: 10.10.10.20
            nameservers:
                addresses: [8.8.4.4,8.8.8.8]

```

Apply
```shell
netplan --debug apply
```

### Install PostgreSQL server
```shell
apt install postgresql -y
```

show information about all PostgreSQL clusters
```shell
pg_lsclusters

Ver Cluster Port Status Owner    Data directory              Log file
12  main    5432 online postgres /var/lib/postgresql/12/main /var/log/postgresql/postgresql-12-main.log
```

### [Install Odoo 13 nightly][1]
executing the following commands as **root**
```shell
wget -O - https://nightly.odoo.com/odoo.key | apt-key add -

echo "deb http://nightly.odoo.com/13.0/nightly/deb/ ./" >> /etc/apt/sources.list.d/odoo.list

apt-get update && apt-get install odoo
```
some output from screen
```
...
Need to get 87.2 MB of archives.
After this operation, 680 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
...
```

show service status
```shell
service odoo status
● odoo.service - Odoo Open Source ERP and CRM
     Loaded: loaded (/lib/systemd/system/odoo.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2020-06-04 07:11:23 UTC; 1min 9s ago
   Main PID: 6790 (odoo)
      Tasks: 4 (limit: 57690)
     Memory: 59.2M
     CGroup: /system.slice/odoo.service
             └─6790 /usr/bin/python3 /usr/bin/odoo --config /etc/odoo/odoo.conf --logfile /var/log/odoo/od>

Jun 04 07:11:23 odoo13 systemd[1]: Started Odoo Open Source ERP and CRM.
```

### Create custom module repository
* [Create repository on **Github**][2] with name `odoo13_cust_mod`

* clone repository `git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY`
```shell
cd /opt
git clone https://github.com/kitcharoenp/odoo13_cust_mod.git
```

* change owner and permit group `odoo` to write
```shell
ls /opt
drwxr-xr-x  4 root root  5 Jun  4 07:29 odoo13_cust_mod
...
chown -R odoo:odoo /opt/odoo13_cust_mod
chmod -R g+w /opt/odoo13_cust_mod
...
ls /opt
drwxrwxr-x  4 odoo odoo  5 Jun  4 07:29 odoo13_cust_mod
```

### Configuring the `addons_path` point to custom module
* backup config
```shell
# backup configuration
cp /etc/odoo/odoo.conf /etc/odoo/odoo.conf.default
```

* append custom module  `/opt/odoo13_cust_mod` to `addons_path`
```shell
# show odoo configuration file
cat /etc/odoo/odoo.conf
...
[options]
; This is the password that allows database operations:
; admin_passwd = admin
db_host = False
db_port = False
db_user = odoo
db_password = False
; append custom module to addons_path
addons_path = /usr/lib/python3/dist-packages/odoo/addons, /opt/odoo13_cust_mod
```

* restart service
```shell
service odoo restart
```

[1]: https://www.odoo.com/documentation/13.0/setup/install.html#repository "Install from Repository"

[2]: https://help.github.com/en/github/getting-started-with-github/create-a-repo "Create a repo"
