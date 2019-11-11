---
layout: post
title: "Install MySQL 8.0"
published: false
categories: [mysql]
---
Notes while reading : [Install MySQL 8.0 on Ubuntu](https://linoxide.com/linux-how-to/install-mysql-ubuntu/)

### Download repository from [MySQL Community Downloads](https://dev.mysql.com/downloads/repo/apt/)
```
# curl -OL https://dev.mysql.com/get/mysql-apt-config_0.8.14-1_all.deb
```

### Install MySQL repository package.
Scroll down and select the last option - **"Ok"**
```
# dpkg -i mysql-apt-config_0.8.14-1_all.deb

# sudo apt update
```

### Install MySQL server
```
# apt install mysql-server
```

### Display the information
```
root@mysql-repl:~# mysqladmin -u root -p version
Enter password:
mysqladmin  Ver 8.0.18 for Linux on x86_64 (MySQL Community Server - GPL)
Copyright (c) 2000, 2019, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Server version		8.0.18
Protocol version	10
Connection		Localhost via UNIX socket
UNIX socket		/var/run/mysqld/mysqld.sock
Uptime:			1 min 22 sec

Threads: 2  Questions: 3  Slow queries: 0  Opens: 115  Flush tables: 3  Open tables: 35  Queries per second avg: 0.036
```

### Securing installation
* Remove anonymous users?
* Disallow root login remotely?
* Remove test database and access to it?

```
# mysql_secure_installation
```
