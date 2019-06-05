---
layout: post
title: "MySQL update column with csv file"
published: true
categories: [mysql]
---
### [MySQL temporary table](http://www.mysqltutorial.org/mysql-temporary-table/)
> In MySQL, a temporary table is a special type of table that allows you to store a temporary result set, which you can reuse several times in a single session.

> A temporary table is very handy when it is impossible or expensive to query data that requires a single SELECT statement with the JOIN clauses

### Creating a MySQL temporary table

```
CREATE TEMPORARY TABLE temp_table LIKE update_table;
```

### Using `LOAD DATA INFILE`
> [Your MySQL server has been started with `--secure-file-priv` option which basically limits from which directories you can load files using LOAD DATA INFILE.](https://stackoverflow.com/questions/32737478/how-should-i-tackle-secure-file-priv-in-mysql)

The [`secure_file_priv`](https://dev.mysql.com/doc/refman/8.0/en/server-options.html#option_mysqld_secure-file-priv) variable is used to limit the effect of data import and export operations.

```sql
mysql> SHOW VARIABLES LIKE "secure_file_priv";
+------------------+-----------------------+
| Variable_name    | Value                 |
+------------------+-----------------------+
| secure_file_priv | /var/lib/mysql-files/ |
+------------------+-----------------------+
1 row in set (0.01 sec)

mysql>
```

* Move csv data file to the directory specified by `secure-file-priv`.

```shell
cp data.csv /var/lib/mysql-files/
```

* Load data

```sql
LOAD DATA INFILE '/var/lib/mysql-files/data.csv'
INTO TABLE temp_table
FIELDS TERMINATED BY ',' (name, update_column);
```

### Update table
```sql
UPDATE update_table
INNER JOIN temp_table
ON temp_table.name = update_table.name  
SET update_table.update_column = temp_table.update_column;
```

Thank this good answer on [Stack Overflow](https://stackoverflow.com/questions/10253605/import-csv-to-update-only-one-column-in-table)
