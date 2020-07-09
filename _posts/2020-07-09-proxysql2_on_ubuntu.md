---
layout: post
title: "ProxySQL on Ubuntu"
---
### [Installing][1]

* **Adding the repository:**
```shell
wget -O - 'https://repo.proxysql.com/ProxySQL/repo_pub_key' | apt-key add -
echo deb https://repo.proxysql.com/ProxySQL/proxysql-2.0.x/$(lsb_release -sc)/ ./ \
| tee /etc/apt/sources.list.d/proxysql.list
```

* **Installing ProxySQL:**
```shell
$ sudo apt update
$ sudo apt install proxysql
$ proxysql --version
ProxySQL version 2.0.12-38-g58a909a0, codename Truls
```

* **Starting ProxySQL:**

status
```shell
$ sudo service proxysql status
● proxysql.service - High Performance Advanced Proxy for MySQL
   Loaded: loaded (/lib/systemd/system/proxysql.service; enabled; vendor preset: enabled)
   Active: inactive (dead)   
```
start
```shell
$ sudo service proxysql start
$ sudo service proxysql status
● proxysql.service - High Performance Advanced Proxy for MySQL
   Loaded: loaded (/lib/systemd/system/proxysql.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-07-09 08:54:45 UTC; 2s ago
  Process: 1141 ExecStart=/usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf $PROXYSQL_O
 Main PID: 1143 (proxysql)
    Tasks: 24 (limit: 4915)
   CGroup: /system.slice/proxysql.service
           ├─1143 /usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf
           └─1144 /usr/bin/proxysql --idle-threads -c /etc/proxysql.cnf
```

### Configuring ProxySQL
> First of all, bear in mind that the best way to configure ProxySQL is through its admin interface [2][2]

```shell
$ mysql -u admin -padmin -h 127.0.0.1 -P6032 --prompt='Admin> '
```

[1]: https://proxysql.com/documentation/installing-proxysql/ "Installing ProxySQL"

[2]: https://proxysql.com/documentation/getting-started/ "Getting started"
