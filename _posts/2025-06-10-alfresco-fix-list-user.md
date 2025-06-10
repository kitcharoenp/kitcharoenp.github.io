---
layout : post
title : "Alfresco: Fix list users in admin tools"
categories : [alfresco, lxc]
published : true
---
### Find 

**share-config**:
```shell
$ find ~/tomcat/ | grep share-config

$TOMCAT_DIR/webapps/share/WEB-INF/classes/alfresco/share-config.xml
$TOMCAT_DIR/webapps/share/WEB-INF/classes/share-config.properties
$TOMCAT_DIR/shared/classes/alfresco/web-extension/share-config-custom.xml
```

### Edit
Edit `share-config.xml` and `share-config-custom.xml`:

Set `show-authorization-status`   to **false**.

```xml
<!-- Default 
   <show-authorization-status>true</show-authorization-status> 
-->
<show-authorization-status>false</show-authorization-status>
```

### Restart tomcat
```shell
$ sudo service tomcat restart
```

### Reference

* [Can't list users in admin tools](https://github.com/Alfresco/acs-community-packaging/issues/367#issuecomment-853122824)