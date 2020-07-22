---
layout: post
title: "ProxySQL2: Read/write split using digest and regex"
categories: [mysql]
---
> Read/write split is perhaps one of the most common type of query routing used, while the other most commonly used query routing implementation is for sharding. [[1]]

### Display hostgroup:
```sql
proxy_admin> SELECT * FROM mysql_replication_hostgroups;
+------------------+------------------+----------------------------+----------+
| writer_hostgroup | reader_hostgroup | check_type                 | comment  |
+------------------+------------------+----------------------------+----------+
| 1                | 2                | read_only|innodb_read_only | cluster1 |
+--
```

##  `stats_mysql_query_digest`

### Find the top 5 queries based on total average time

```sql
SELECT hostgroup hg, digest, count_star,
sum_time/count_star avg_time, SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
ORDER BY avg_time DESC LIMIT 5;
```
**Result:**
```
+----+--------------------+------------+----------+-------------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                                    |
+----+--------------------+------------+----------+-------------------------------------------------------------+
| 1  | 0x4A0039C1F5C1267F | 535        | 20899698 | select `auditevent`.`id` id from `auditevent` where (`audit |
| 1  | 0x5D1F532AC72A9997 | 11         | 20311109 | select `auditevent`.`id` id from `auditevent` where (`audit |
| 1  | 0x994DE820C17EF214 | 11         | 18106211 | select count(distinct `auditevent`.`id`) from `auditevent`  |
| 1  | 0x5B604AFD2D1D7527 | 1          | 15149769 | select count(`serviceorder`.`id`) col0, `serviceorder`.`id` |
| 1  | 0x33F44352403A1969 | 4          | 15091698 | select count(`serviceorder`.`id`) col0, `serviceorder`.`id` |
+----+--------------------+------------+----------+-------------------------------------------------------------+
```

### Set expensive query digest to `reader_hostgroup`

**Create query rule**
```sql
INSERT INTO mysql_query_rules
(rule_id,active,digest,destination_hostgroup,apply,comment)
VALUES
(1,1,'0x4A0039C1F5C1267F',2,1,'select `auditevent`.`id');
```

**Load rule to runtime**
```sql
LOAD MYSQL QUERY RULES TO RUNTIME;
SAVE MYSQL QUERY RULES TO DISK;
```


### Find the top 5 queries based on count

```sql
SELECT hostgroup hg, digest, count_star,
sum_time/count_star avg_time,
SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
ORDER BY count_star DESC LIMIT 5;
```

**Result:**
```
+----+--------------------+------------+----------+-------------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                                    |
+----+--------------------+------------+----------+-------------------------------------------------------------+
| 1  | 0xCD135403E429330F | 1230552    | 633      | SELECT * FROM `customfield` WHERE ( `id` IN ( ?) )          |
| 1  | 0x77845D920877E661 | 903363     | 618      | SELECT * FROM `basecustomfield` WHERE ( `id` IN ( ?) )      |
| 1  | 0x491A4E8F8CE8FD0D | 832594     | 910      | SELECT * FROM `_user` WHERE username = ?                    |
| 1  | 0xAF23BB94F87200A9 | 608192     | 586      | select id from _user where username = ?                     |
| 1  | 0xFFBEDA9BDDFE9D44 | 600168     | 6506     | select `_user`.`id` id from (`_user`, `person`) left join ` |
+----+--------------------+------------+----------+-------------------------------------------------------------+
```


### Find the top 5 queries based on digest_text start with `'SELECT * FROM%'`

```sql
SELECT hostgroup hg, digest, count_star,
sum_time/count_star avg_time,
SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
WHERE digest_text LIKE 'SELECT * FROM%'
ORDER BY count_star DESC LIMIT 5;
```

**Result:**
```
+----+--------------------+------------+----------+--------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                               |
+----+--------------------+------------+----------+--------------------------------------------------------+
| 1  | 0xCD135403E429330F | 1231223    | 633      | SELECT * FROM `customfield` WHERE ( `id` IN ( ?) )     |
| 1  | 0x77845D920877E661 | 903642     | 618      | SELECT * FROM `basecustomfield` WHERE ( `id` IN ( ?) ) |
| 1  | 0x491A4E8F8CE8FD0D | 832885     | 910      | SELECT * FROM `_user` WHERE username = ?               |
| 1  | 0x405AD87076FBC718 | 355927     | 912      | SELECT * FROM `_user` WHERE ( `id` IN ( ?) )           |
| 1  | 0x49ED076ADC1D7E11 | 303844     | 658      | SELECT * FROM `item` WHERE ( `id` IN ( ?) )            |
+----+--------------------+------------+----------+--------------------------------------------------------+
```

### Create query rule
 All the queries starting with `SELECT * FROM` can go to `hostgroup 2`
```sql
INSERT INTO mysql_query_rules
(rule_id,active,match_digest,destination_hostgroup,apply,comment)
VALUES
(2,1,'^SELECT \* FROM',2,1,'SELECT * FROM');
```

**Load rule to runtime**
```sql
LOAD MYSQL QUERY RULES TO RUNTIME;

--if you want this change to be permanent
SAVE MYSQL QUERY RULES TO DISK;
```

###  Reset the contents of the table `stats_mysql_query_digest`
To achieve this we can simply run any query against `stats_mysql_query_digest_reset`
```sql
SELECT * FROM stats_mysql_query_digest_reset LIMIT 1;
```


### Show result after apply rule
```sql
SELECT hostgroup hg, digest, count_star,
sum_time/count_star avg_time, SUBSTR(digest_text,0,60)
FROM stats_mysql_query_digest
WHERE digest_text LIKE 'SELECT * FROM%'  
LIMIT 5;
```
**Result:**
```
+---+--------------------+------------+----------+-------------------------------------------------------------+
| hg | digest             | count_star | avg_time | SUBSTR(digest_text,0,60)                                    |
+----+--------------------+------------+----------+-------------------------------------------------------------+
| 2  | 0x65DBC430D1719490 | 1          | 455      | SELECT * FROM `customerorder` WHERE id = ?                  |
| 2  | 0xB1C07AC2FC307E3A | 1          | 18625    | SELECT * FROM `account_project` WHERE ( `project_id` IN ( ? |
| 2  | 0x3482CA73C1D926ED | 1          | 494      | SELECT * FROM `project` WHERE id = ?                        |
| 2  | 0x841589C12E003A69 | 4          | 703      | SELECT * FROM `gamescore` WHERE ( `id` IN ( ?,?,?,?,?,?,?,? |
| 2  | 0x15FFD00F5FD983BD | 2          | 570      | SELECT * FROM `operationalbill` WHERE ( `id` IN ( ?) )      |
+----+--------------------+------------+----------+-------------------------------------------------------------+
```

### Reference
[[1]] [Read/Write Split][1]


[1]: https://proxysql.com/documentation/proxysql-read-write-split-howto/ "Read/Write Split"
