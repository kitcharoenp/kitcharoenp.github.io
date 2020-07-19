---
layout: post
title: "ProxySQL2: Read/write split using digest"
categories: [mysql]
---
## Find expensive queries using `stats_mysql_query_digest`

### Find the top 5 queries based on total average time

**find expensive digest:**
```sql
SELECT hostgroup hg, digest, count_star, sum_time/count_star avg_time, SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
ORDER BY avg_time DESC LIMIT 5;
```
**result:**
```
+----+--------------------+------------+----------+-------------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                                    |
+----+--------------------+------------+----------+-------------------------------------------------------------+
| 1  | 0x4A0039C1F5C1267F | 5          | 21979585 | select `auditevent`.`id` id from `auditevent` where (`audit |
| 1  | 0x5D1F532AC72A9997 | 1          | 20118996 | select `auditevent`.`id` id from `auditevent` where (`audit |
| 1  | 0x994DE820C17EF214 | 1          | 18206542 | select count(distinct `auditevent`.`id`) from `auditevent`  |
| 1  | 0xBDC9A57A7144576B | 1          | 1012782  | select `warehousemanagement`.`id` id from `warehousemanagem |
| 1  | 0xADD2FCD995A27780 | 7          | 970747   | select `materialscontrol`.`id` id from (`materialscontrol`, |
+----+--------------------+------------+----------+-------------------------------------------------------------+
```

### Set expensive query digest to `reader_hostgroup`

**display hostgroup:**
```sql
proxy_admin> SELECT * FROM mysql_replication_hostgroups;
+------------------+------------------+----------------------------+----------+
| writer_hostgroup | reader_hostgroup | check_type                 | comment  |
+------------------+------------------+----------------------------+----------+
| 1                | 2                | read_only|innodb_read_only | cluster1 |
+--
```

**create query rule**
```sql
INSERT INTO mysql_query_rules
(rule_id,active,digest,destination_hostgroup,apply,comment)
VALUES
(1,1,'0x4A0039C1F5C1267F',2,1,'select `auditevent`.`id');
```

**load rule to runtime**
```sql
LOAD MYSQL QUERY RULES TO RUNTIME;
SAVE MYSQL QUERY RULES TO DISK; # if you want this change to be permanent
```


### Find the top 5 queries based on count

**find digest:**
```sql
SELECT hostgroup hg, digest, count_star, sum_time/count_star avg_time, SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
ORDER BY count_star DESC LIMIT 5;
```

**result:**
```
+----+--------------------+------------+----------+-------------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                                    |
+----+--------------------+------------+----------+-------------------------------------------------------------+
| 1  | 0xCD135403E429330F | 1447       | 648      | SELECT * FROM `customfield` WHERE ( `id` IN ( ?) )          |
| 1  | 0x0A94A3EA5EB6B26A | 1372       | 1116     | SELECT * FROM `globalmetadata` WHERE className = ?          |
| 1  | 0xAEEF848F45EA7632 | 1353       | 6347     | SELECT * FROM `perusermetadata` WHERE className = ? and _us |
| 1  | 0x405AD87076FBC718 | 1204       | 922      | SELECT * FROM `_user` WHERE ( `id` IN ( ?) )                |
| 1  | 0x36F92351D29537E4 | 1094       | 667      | SELECT * FROM `_group` WHERE name = ?                       |
+----+--------------------+------------+----------+-------------------------------------------------------------+
```
