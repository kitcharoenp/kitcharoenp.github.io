---
layout : post
title : "Alfresco 7.0 Docker: Error CSRF attack noted when asserting referer header"
categories : [alfresco, docker]
published : true
---

### [CSRF attack noted when asserting referer header](https://hub.alfresco.com/t5/alfresco-content-services-forum/community-7-0-possible-csrf-attack-noted-when-asserting-referer/td-p/307430)

  
### `share-config-custom.xml`

```shell

$ docker exec -it docker-compose-share-1 bash
$ pwd
/usr/local/tomcat

# Find `share-config-custom.xml` location

$ find | grep share-config-custom
./shared/classes/alfresco/web-extension/share-config-custom.xml
```
