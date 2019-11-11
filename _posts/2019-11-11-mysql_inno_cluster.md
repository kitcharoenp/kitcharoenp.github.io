---
layout: post
title: "MySQL InnoDB Cluster"
published: false
categories: [mysql]
---
Notes while reading :

> ### [Replication Compatibility Between MySQL Versions](https://dev.mysql.com/doc/mysql-replication-excerpt/5.7/en/replication-compatibility.html)
> MySQL supports replication from one release series to the next higher release series. For example, you can replicate from a master running MySQL 5.6 to a slave running MySQL 5.7, **from a master running MySQL 5.7 to a slave running MySQL 8.0**, and so on.

> ### [Migration from MySQL Master-Slave pair to MySQL InnoDB Cluster: howto](https://lefred.be/content/migration-from-mysql-master-slave-pair-to-mysql-innodb-cluster-howto/)
> * replication from current system
> * creation of the cluster with a single instance (using MySQL Shell)
> * adding instances to the cluster
> * configure the router
> * test phase
> * pointing the application to the new solution

### MySQL InnoDB Cluster – howto install it from scratch

Get a temporay password from mysqld.log after fresh installation mysql
```
grep passwd /var/log/mysqld.log
```
