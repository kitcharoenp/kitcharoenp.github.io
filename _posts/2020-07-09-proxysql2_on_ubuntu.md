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

### Configuring
> First of all, bear in mind that the best way to configure ProxySQL is through its admin interface [2][2]

* **Install mysql-client:**
  ```shell
  $ sudo apt install mysql-client
  ```   

* **Changing the mysql prompt:**

  Open your MySQL **my.cnf**, add the following line in the `[mysql]` section. If the file does not have one, create it by adding the following line.

  **my.cnf:**
  ```
  [mysql]

  prompt=\u@\h:[\d]>\_
  ```

* **Connect proxysql:**
  ```shell
  $ mysql -u admin -padmin -h 127.0.0.1 -P6032
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 4
  Server version: 5.5.30 (ProxySQL Admin Module)
  ...
  admin@127.0.0.1:[(none)]>
  ```

[1]: https://proxysql.com/documentation/installing-proxysql/ "Installing ProxySQL"

[2]: https://proxysql.com/documentation/getting-started/ "Getting started"

[3]: https://proxysql.com/documentation/ProxySQL-Configuration/ "configure ProxySQL"
