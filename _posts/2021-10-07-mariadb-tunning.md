---
layout : post
title : "MariaDB : Tunning"
categories : [mariadb]
published : false
---

### [Avoid Swapping][1]
Obviously, accessing swap memory from disk is far slower than accessing RAM directly. The main way to avoid swapping is to make sure you have enough RAM for all processes that need to run on the machine. The default is usually 60 - you can check this by running:

```shell
sysctl vm.swappiness

# change value without reboot
sysctl -w vm.swappiness=1
```

You can change the default by adding a line to the sysctl.conf file (usually found in `/etc/sysctl.conf`).

```
vm.swappiness = 1
```

A low swappiness setting is recommended for database workloads. For MariaDB databases, it is recommended to set swappiness to a value of 1.


[1]: https://mariadb.com/kb/en/configuring-swappiness/ "Avoid Swapping"


### Thread Pool


Configuring the Thread Pool on Unix
```
[mariadb]
...
thread_handling=pool-of-threads
```

[2]: https://mariadb.com/kb/en/thread-pool-in-mariadb/ "Thread Pool"