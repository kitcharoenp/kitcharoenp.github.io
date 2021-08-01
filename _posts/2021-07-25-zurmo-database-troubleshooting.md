---
layout : post
title : "Zurmo Database Troubleshooting"
categories : [zurmo, mariadb]
published : true
---

#### [ERROR 1419 (HY000):][1]  You do not have the SUPER privilege ..
You do not have the SUPER privilege and
binary logging is enabled (you *might* want to use the less safe
log_bin_trust_function_creators variable)

**Solution:** Set the global `log_bin_trust_function_creators` system variable to 1.

```sql
SET GLOBAL log_bin_trust_function_creators = 1;
```
or
```
## my.cnf
log_bin_trust_function_creators = 1
```

#### innodb_buffer_pool_size
Typical values are 5-6GB (8GB RAM), 20-25GB (32GB RAM), 100-120GB (128GB RAM).

```
innodb_buffer_pool_size=10G
```

#### read_only
Make all non-temporary tables read-only, with the exception for replication (slave) threads and users with the SUPER privilege.

```
read_only=ON
```

#### character-set-server

```
character-set-server=utf8
collation-server=utf8_unicode_ci
```

#### max_sp_recursion_depth
The number of times that any given stored procedure may be called recursively. Stored procedure recursion increases the demand on thread stack space. If you increase the value of **max_sp_recursion_depth**, it may be necessary to increase **thread_stack** size by increasing the value of thread_stack at server startup.

The stack size for each thread.  If the thread stack size is too small,
it limits the complexity of the SQL statements that the server can handle,
the recursion depth of stored procedures, and other memory-consuming actions.

```
max_sp_recursion_depth=200
thread_stack=512K
```

#### optimizer-search-depth
The maximum depth of search performed by the query optimizer.
If set to 0, the system automatically picks a reasonable value.

```
optimizer-search-depth=0
```

### Remote connect  (111 "Connection refused")
```
# comment `bind-address` for fixed error
# bind-address          = 127.0.0.1  
```

### instance read only
```sql
# check read only mode:
SELECT @@global.read_only;
```

```sql
# set at Runtime
SET GLOBAL read_only=1;
```

### SQLSTATE[22007]: Invalid datetime format: 1292 Incorrect datetime

>The reason for this by default MariaDB’s sql mode has NO_ZERO_DATE which won’t allow zero dates to be entered only valid or null is allowed. \[[2]\]

**current varialble of mariadb 10.2**
```sql
SELECT @@SQL_MODE, @@GLOBAL.SQL_MODE \G
*************************** 1. row ***************************
       @@SQL_MODE: STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
@@GLOBAL.SQL_MODE: STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

Fixed by edit **my.cnf**

```
sql_mode=NO_AUTO_CREATE_USER
```



[1]: https://stackoverflow.com/questions/56389698/why-super-privileges-are-disabled-when-binary-logging-option-is-on "EO[2]R 1419 (HY000)"

[2]: https://dcblog.dev/mariabdb-avoid-invalid-datetime-format-when-date-is-empty "Invalid datetime format"
