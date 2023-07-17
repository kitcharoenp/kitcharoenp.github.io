---
layout : post
title : "Alfresco6.x CE Tunning"
categories : [alfresco]
published : false
---

### [Using `alfresco-global.properties`](https://docs.alfresco.com/content-services/community/config/#using-alfresco-globalproperties)
> The global properties `alfresco-global.properties` file contains the customizations for extending Community Edition. Tomcat, browse to the `$TOMCAT_HOME/shared/classes/` directory.

### Disable Un-used services and features

```
# Disable virtual file-systems
#
ftp.enabled=false
webdav.enabled=false
nfs.enabled=false
imap.enabled=false

# Disable thumbnails and documents previews
#
system.thumbnail.generate=false
```

### [Disable content replication](https://docs.alfresco.com/content-services/6.0/admin/replication/)
> Automatically replicate folders and content between repositories using replication jobs.
> Replication suits an environment where you’re running multiple, separate instances of Content Services and then replicating a subset of the content between these servers.

```
# Disable content replication
#
replication.enabled=false
transferservice.receiver.enabled=false
```

### [Disable Content Services features](https://docs.alfresco.com/content-services/6.0/config/#disable-content-services-features)

```
# Disable Content Services features

# Disables quotas or user usages.
system.usages.enabled=false
system.usages.clearBatchSize=0

# Specifies a way to globally enable or disable the auditing framework.
audit.enabled=false

# Specifies whether to enable or disable the CIFS server.
cifs.enabled=false 

# Disable cloud-sync features
#
# Use this property to disable synchronization permanently.
syncService.mode=OFF
sync.mode=OFF
sync.pullJob.enabled=false
sync.pushJob.enabled=false

# Disables generation of audit data.
audit.alfresco-access.enabled=false

# Disables the eager creation of home folders.
home.folder.creation.eager=false

# Disables the creation of home folders.
home.folder.creation.disabled=true

# Specifies whether the system bootstrap should create or upgrade the database schema automatically.
db.schema.update=false

# Disables the Share Activities email notification.
activities.feed.notifier.enabled=false
activities.feed.cleaner.enabled=false
activities.post.cleaner.enabled=false
```

### [Cleaning up binary files](https://docs.alfresco.com/content-services/7.0/admin/content-stores/)

```
system.content.orphanProtectDays=7
```


## Solr Tunning

### [Disable Solr 4 Tracking](https://hub.alfresco.com/t5/alfresco-content-services-blog/using-ssl-with-alfresco-search-services-and-solr-6/ba-p/292687)

Go to each Solr 4 core and edit `<core>/conf/solrcore.properties` to set:
```
enable.alfresco.tracking=false
```

SOLR application has two cores preconfigured, one for the workspace and other one for the archive (which is not needed usually). The configuration can be found in solrcore.properties files:

 ```
${dir.root}/solr4/workspace-SpacesStore/conf/solrcore.properties
${dir.root}/solr4/archive-SpacesStore/conf/solrcore.properties
 ```

### [To avoid indexing full content in Alfresco](https://armedia.com/blog/alfresco-indexing-document-metadata-only-confirmation-with-luke/)

To avoid indexing full content in Alfresco. In solrcore.properties (for workspace and archive store) set: 

```
# Disable full-text indexing
#
# disables content indexing for all documents that are introduced into the system.
#
alfresco.index.transformContent=false
alfresco.ignore.datatype.1=d:content
```

### [Disable SOLR Document Cache](https://docs.alfresco.com/search-services/latest/config/performance/#disable-solr-document-cache)
disabled in order to decrease the use of RAM memory.

```
# Disable SOLR Document Cache
#
solr.documentCache.size=0
solr.documentCache.initialSize=0
solr.documentCache.autowarmCount=0
```

### Disable SSL to reduce complexity

```
# Disable SSL to reduce complexity
#
# none, https : default(https)
# alfresco.secureComms=https
```
if set `alfresco.secureComms=none` , a new add user can't found on a site select member

### SOLR admin
SOLR admin panel are related to get info about the health of SOLR : 
`https://alfserver:8443/solr/admin/cores/action=SUMMARY`


### [Optimize SOLR Index](https://docs.alfresco.com/search-services/latest/config/performance/#optimize-solr-index)

### Reference

* [Alfresco tuning part1](https://www.slideshare.net/LuisCabaceira/alfresco-tuning-part1-54221871)

* [Alfresco tuning part2](https://www.slideshare.net/LuisCabaceira/alfresco-tuning-part2)

* [Search Subsystem in Alfresco](https://www.zylk.net/en/web-2-0/blog/-/blogs/notes-about-search-subsystem-in-alfresco)

* [Solr avoid indexing full content in Alfresco](https://www.zylk.net/en/web-2-0/blog/-/blogs/how-to-avoid-indexing-full-content-in-alfresco)


