---
layout: post
title: "ProxySQL2: Read/write split using digest"
categories: [mysql]
---
## Find expensive queries using `stats_mysql_query_digest`



```
INSERT INTO mysql_query_rules (rule_id,active,digest,destination_hostgroup,apply)
VALUES
(1,1,'0x4A0039C1F5C1267F',2,1);
```
