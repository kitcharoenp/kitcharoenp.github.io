---
layout : post
title : "MariaDB Changing Global Variable"
categories : [mariadb, replication]
published : true
---

### [innodb_buffer_pool_size][1]

```sql
-- set innodb_buffer_pool_size = 10G
SET GLOBAL innodb_buffer_pool_size=(10 * 1024 * 1024 * 1024);

-- show global status
SHOW GLOBAL STATUS LIKE 'Innodb_buffer_pool_resize_status';
+----------------------------------+----------------------------------------------------+
| Variable_name                    | Value                                              |
+----------------------------------+----------------------------------------------------+
| Innodb_buffer_pool_resize_status | Completed resizing buffer pool at 210727 16:30:38. |
+----------------------------------+----------------------------------------------------+
```

[1]: https://mariadb.com/kb/en/setting-innodb-buffer-pool-size-dynamically/ "Setting Innodb Buffer Pool Size Dynamically"
