---
layout : post
title : "MariaDB : remove user account"
categories : [mariadb]
published : true
---

```

mariadb> SELECT User,Host FROM mysql.user;

mariadb> SHOW GRANTS FOR 'bloguser'@'localhost';

mariadb> REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'bloguser'@'localhost';


mariadb> DROP USER 'bloguser'@'localhost';

```