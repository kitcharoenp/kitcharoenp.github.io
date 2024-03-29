---
layout : post
title : "Zurmo : my.cnf"
categories : [zurmo]
published : false
---

**/etc/mysql/my.cnf:**
```
[mysqld]

#
# * Fine Tuning
#

sort_buffer_size        = 4M
bulk_insert_buffer_size = 16M
tmp_table_size          = 32M
max_heap_table_size     = 32M

#
# * Zurmo tunning
#

character-set-server    = utf8
collation-server        = utf8_unicode_ci
max_sp_recursion_depth  = 200
thread_stack            = 512K
log_bin_trust_function_creators = 1

# Fixed 'Could not find first log file name in binary log index file'

expire_logs_days        = 0

# set to 0, the system automatically picks a reasonable value.

optimizer_search_depth  = 0

# Fixed non-compaitble with NO_ZERO_DATE and etc.

sql_mode                = "NO_AUTO_CREATE_USER"

#
# * Query Cache Configuration
#

query_cache_type        = 0
query_cache_size        = 0


# Skip reverse DNS lookup 
skip-name-resolv        = ON


[mariadb]
#
# * Replication
#
log-bin
server_id               = 1030
read_only


#
# * Thread Pool
#
thread_handling=pool-of-threads


#
# * InnoDB
#
default_storage_engine  = InnoDB
innodb_buffer_pool_size = 16G 
innodb_io_capacity      = 400

#
# * Tunning for ZFS 
#
innodb_log_write_ahead_size     = 16384
innodb_doublewrite              = 0
innodb_flush_neighbors          = 0
innodb_use_native_aio           = 0
innodb_use_atomic_writes        = 0
innodb_flush_log_at_trx_commit  = 0
innodb_compression_default      = OFF
sync_binlog                     = 0
innodb_file_per_table           = ON
```


### [Get size of a mysql database][c]

```sql
SELECT table_schema "zurmo",
        ROUND(SUM(data_length + index_length) / 1024 / 1024, 1) "DB Size in MB" 
FROM information_schema.tables 
GROUP BY table_schema; 
```

[c]: https://stackoverflow.com/questions/1733507/how-to-get-size-of-mysql-database "Get size of a mysql database"


### Query caching 

> Query caching can give significant performance improvements when used correctly and/or in conjunction with `Memcached` or `Redis` cache. \[[b]\]

> As mentioned, the key is, when you start tuning your MySQL query cache size, `start small`. You should adjust your “query_cache_limit” because the default of **1 megabyte may be too large**. Allowing your cache to fill up too fast creates lots of cache prunes. Also, have a look at adjusting the “query_cache_min_res_unit” tuning parameter to combat cache fragmentation. \[[b]\]

> However, eventually, MySQL Query Cache was completely disabled on that 32GB server for improved performance): \[[b]\]

```
query_cache_type                = 0
query_cache_size                = 0
```

[b]: https://haydenjames.io/mysql-query-cache-size-performance/ "query-cache-size-performance"


> To disable the query cache at server startup, set the query_cache_size system variable to 0. By disabling the query cache code, there is no noticeable overhead. \[[a]\]


[a]: https://dev.mysql.com/doc/refman/5.7/en/query-cache.html "query-cache"

Disable the MySQL query cache without restarting MySQL

```
SET GLOBAL query_cache_type = 0;
SET GLOBAL query_cache_size = 0;
```

**query_cache:**
```sql
MariaDB [(none)]> show variables like 'query_cache_%';
+------------------------------+----------+
| Variable_name                | Value    |
+------------------------------+----------+
| query_cache_limit            | 1048576  |
| query_cache_min_res_unit     | 4096     |
| query_cache_size             | 67108864 |
| query_cache_strip_comments   | OFF      |
| query_cache_type             | ON       |
| query_cache_wlock_invalidate | OFF      |
+------------------------------+----------+
6 rows in set (0.001 sec)
```