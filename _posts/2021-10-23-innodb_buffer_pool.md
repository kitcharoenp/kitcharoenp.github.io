---
layout : post
title : "InnoDB : innodb_buffer_pool_size"
categories : [mysql, mariab]
published : false
---
```sql
SELECT CEILING(Total_InnoDB_Bytes*1.6/POWER(1024,3)) RIBPS FROM
(SELECT SUM(data_length+index_length) Total_InnoDB_Bytes
FROM information_schema.tables WHERE engine='InnoDB') A;
```


> This will give you the RIBPS, Recommended InnoDB Buffer Pool Size, based on all InnoDB Data and Indexes, with an additional 60%. With this output, you would set the following in /etc/my.cnf \[[1]\]


After the restart, run MySQL for a week or two. Then, run this query:

```sql
SELECT (PagesData*PageSize)/POWER(1024,3) DataGB FROM
(SELECT variable_value PagesData
FROM information_schema.global_status
WHERE variable_name='Innodb_buffer_pool_pages_data') A,
(SELECT variable_value PageSize
FROM information_schema.global_status
WHERE variable_name='Innodb_page_size') B;
```

> This will give you how many actual GB of memory is in use by InnoDB Data in the InnoDB Buffer Pool at this moment. \[[2]\]


[1]: https://itectec.com/database/thesql-how-large-should-be-thesql-innodb_buffer_pool_size/ "How large should be thesql innodb_buffer_pool_size"

[2]: https://dba.stackexchange.com/questions/19164/what-to-set-innodb-buffer-pool-and-why/19181#19181 "What to set innodb_buffer_pool and why..?"