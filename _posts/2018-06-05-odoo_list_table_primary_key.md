---
layout: post
title:  "Odoo List"
date:   2018-06-05 12:31:01 +0700
categories: [postgresql]
published: true
---

### List primary keys for all tables
Get the idea from this [link](https://dba.stackexchange.com/questions/11032/list-primary-keys-for-all-tables-postgresql)
```
select
  tc.table_schema,
  tc.table_name,
  kc.column_name
from information_schema.table_constraints tc
  join information_schema.key_column_usage kc
    on kc.table_name = tc.table_name
    and kc.table_schema = tc.table_schema
    and kc.constraint_name = tc.constraint_name
where tc.constraint_type = 'PRIMARY KEY';
```

### Analyze a Postgres database
*  `-Z`  Only calculate statistics for use by the optimizer (no vacuum).
*  `-v`  Print detailed information during processing.
*  `-a`  Vacuum all databases.
*  `-j 6`Execute the vacuum or analyze commands in parallel by running njobs (6) commands simultaneously.
```
vacuumdb -Zv -a -j 6
```
