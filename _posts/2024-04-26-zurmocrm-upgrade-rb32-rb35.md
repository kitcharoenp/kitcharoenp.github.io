---
layout : post
title : "ZurmoCRM Upgrade redbean v3.2 to v3.5"
categories : [crm]
published : true
---

### Access to undeclared static property: ZurmoRedBean::$exporter

**/var/www/html/zurmo/app/protected/core/models/RedBeanDatabase.php:**

Find and comment `ZurmoRedBean::$exporter = null;`
```php
public static function close()
{
   ...
   # ZurmoRedBean::$exporter           = null;
```


### TypeError: Argument 1 passed to RedBeanModel::makeModel() must be an instance of RedBean_OODBBean

* **File:** core/models | `GlobalMetadata.php`

```php
--- a/app/protected/core/models/GlobalMetadata.php
+++ b/app/protected/core/models/GlobalMetadata.php
@@ -49,7 +49,7 @@
             assert('$className != ""');
             $bean = ZurmoRedBean::findOne('globalmetadata', "className = '$className'");
             assert('$bean === false || $bean instanceof RedBean_OODBBean');
-            if ($bean === false)
+            if ($bean === false || !is_object($bean))
             {
                 throw new NotFoundException();
             }
```

### Error: Call to undefined method RedBean_QueryWriter_MySQL::getColumnsWithDetails() 
* **Action:** command `php updateSchema super`
* **File:** `CreateOrUpdateExistingTableFromSchemaDefinitionArrayUtil.php:101`
    
    ```php
    try
    {
        $existingFields     = ZurmoRedBean::$writer->getColumnsWithDetails($tableName);
    }
    ```
* **Class:** `ZurmoMySqlQueryWriter::getColumnsWithDetails`
* **Cause:** `ZurmoMySqlQueryWriter` is not extends `RedBean_QueryWriter_MySQL`
* **Solution:** Copy all method on `ZurmoMySqlQueryWriter` append `RedBean_QueryWriter_MySQL` class in `rb.php`


### Reference
* [Old versions](https://redbeanphp.com/index.php?p=/archives)