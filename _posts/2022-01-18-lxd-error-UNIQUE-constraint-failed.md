---
layout : post
title : "Error: Create instance: Insert volume snapshot: UNIQUE constraint failed"
categories : [lxd, lxc]
published : true
---

### [UNIQUE constraint failed](https://discuss.linuxcontainers.org/t/request-for-help-unique-constraint-failed-storage-volumes-snapshots-id/7106)


```shell 
# command
lxd sql global "SELECT MAX(id) FROM storage_volumes_all LIMIT 1;"

# output
+---------+
| MAX(id) |
+---------+
| 3502    |
+---------+

# command 
# set seq as MAX(id) value
lxd sql global "UPDATE sqlite_sequence SET seq = 3502 WHERE name = 'storage_volumes'"
```


