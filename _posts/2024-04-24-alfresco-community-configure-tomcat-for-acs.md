---
layout : post
title : "Alfresco Community 23.x Configure Tomcat for ACS"
categories : [alfreco]
published : true
---

### Copy `web-server/conf`

```shell
# check 
$ ls -lt /home/ubuntu/acs-community-23.2.1/web-server/conf/
total 9
drwxr-xr-x 3 ubuntu ubuntu 3 Feb 29 16:09 Catalina

# copy
$ sudo cp -R /home/ubuntu/acs-community-23.2.1/web-server/conf/* /usr/local/alfresco-community23x/tomcat/conf/

# validate
$ ls  -lt /usr/local/alfresco-community23x/tomcat/conf/
total 103
drwxr-xr-x 3 root root          3 Apr 24 08:20 Catalina
-rwxr-xr-x 1 root Alfresco   1411 Apr 23 09:17 context.xml
-rwxr-xr-x 1 root Alfresco   1149 Apr 23 09:17 jaspic-providers.xml
-rwxr-xr-x 1 root Alfresco   2313 Apr 23 09:17 jaspic-providers.xsd
```

### Copy `web-server/lib`
Directory contains the **PostgreSQL JDBC jar** `postgresql-42.6.0.jar`

```shell
# check
$ $ ls /home/ubuntu/acs-community-23.2.1/web-server/lib/
postgresql-42.6.0.jar

# copy
$ sudo cp /home/ubuntu/acs-community-23.2.1/web-server/lib/* /usr/local/alfresco-community23x/tomcat/lib/

# change group
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/tomcat/lib

# change mode
$ sudo chmod 755 /usr/local/alfresco-community23x/tomcat/lib/*.jar

# validate
$ ls -lt /usr/local/alfresco-community23x/tomcat/lib/
total 11791
-rwxr-xr-x 1 root Alfresco 1081604 Apr 24 08:25 postgresql-42.6.0.jar
-rwxr-xr-x 1 root Alfresco   68483 Apr 23 09:17 tomcat-i18n-cs.jar
-rwxr-xr-x 1 root Alfresco   77385 Apr 23 09:17 tomcat-i18n-de.jar
-rwxr-xr-x 1 root Alfresco  103983 Apr 23 09:17 tomcat-i18n-es.jar
-rwxr-xr-x 1 root Alfresco  175495 Apr 23 09:17 tomcat-i18n-fr.jar
-rwxr-xr-x 1 root Alfresco  199387 Apr 23 09:17 tomcat-i18n-ja.jar
```

### Copy `web-server/shared/classes`

* `alfresco-global.properties.sample:` Sample alfresco global properties file, which is used for configuration properties.

* `alfresco:` Contains the directory structure for the configuration override files, including the extension, and web-extension directories.

```shell
# check
$ ls /home/ubuntu/acs-community-23.2.1/web-server/shared/classes
alfresco  alfresco-global.properties.sample

# copy
$ sudo cp -R /home/ubuntu/acs-community-23.2.1/web-server/shared/classes /usr/local/alfresco-community23x/tomcat/shared/classes/
```

### Config Encryption keystore in `tomcat/shared`

```shell
# create directory
$ sudo mkdir -p /usr/local/alfresco-community23x/tomcat/shared/classes/alfresco/extension/keystore

# check 
ls -lt /home/ubuntu/acs-community-23.2.1/keystore/metadata-keystore
total 10
-rw-r--r-- 1 ubuntu ubuntu 645 Feb 26 14:00 keystore
-rw-r--r-- 1 ubuntu ubuntu 359 Feb 26 14:00 keystore-passwords.properties

# copy `metadata-keystore` directory
$ sudo cp -R /home/ubuntu/acs-community-23.2.1/keystore/metadata-keystore /usr/local/alfresco-community23x/tomcat/shared/classes/alfresco/extension/keystore

```

Metadata encryption keys needs to be now set using `JAVA_TOOL_OPTIONS` java environment variable. For example:

```
JAVA_TOOL_OPTIONS="-Dencryption.keystore.type=JCEKS -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding -Dencryption.keyAlgorithm=DESede -Dencryption.keystore.location=/usr/local/alfresco-community23x/tomcat/shared/classes/alfresco/extension/keystore/metadata-keystore/keystore -Dmetadata-keystore.password=mp6yc0UD9e -Dmetadata-keystore.aliases=metadata -Dmetadata-keystore.metadata.password=oKIWzVdEdA -Dmetadata-keystore.metadata.algorithm=DESede"
```

### Update `tomcat/shared` the permissions
```shell
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/tomcat/shared
$ sudo chmod -R 755 /usr/local/alfresco-community23x/tomcat/shared        
```

### Copy `web-server/webapps`

* **alfresco.war**: Alfresco repository web application.
* **share.war**: Share interface web application.
* **ROOT.war**: Application for the server root, also contains additional code/setup for handling alfresco office services module (alfresco-office-services). Alfresco Office Services (AOS) allow you to access Alfresco Content Services directly from all your Microsoft Office applications. We will not be installing this module since SSL is a mandatory enablement for this module.
* **_vti_bin.war**: App to help and support AOS modul

```shell
# check
$ ls -lt /usr/local/alfresco-community23x/tomcat/webapps/
total 45
drwxrwxr-x  6 root Alfresco  9 Apr 23 09:17 manager
drwxrwxr-x  7 root Alfresco  8 Apr 23 09:17 examples
drwxrwxr-x  6 root Alfresco  7 Apr 23 09:17 host-manager
drwxrwxr-x 16 root Alfresco 61 Apr 23 09:17 docs
drwxrwxr-x  3 root Alfresco 13 Apr 23 09:17 ROOT

# delete all files in `tomcat/webapps/`
$ sudo rm -rf /usr/local/alfresco-community23x/tomcat/webapps/*

# check
$ ls /home/ubuntu/acs-community-23.2.1/web-server/webapps/
ROOT.war  _vti_bin.war  alfresco.war  share.war


# copy all `war` file in `web-server/webapps/` to `tomcat/webapps/`
$ sudo cp /home/ubuntu/acs-community-23.2.1/web-server/webapps/*.war /usr/local/alfresco-community23x/tomcat/webapps/

# update permissions
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/tomcat/webapps/
$ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/webapps/*.war

# check permissions
$ ls -lt /usr/local/alfresco-community23x/tomcat/webapps/
total 238296
drwxr-x--- 14 alfresco Alfresco        18 Apr 24 09:06 share
drwxr-x--- 10 alfresco Alfresco        13 Apr 24 09:06 alfresco
drwxr-x---  4 alfresco Alfresco         5 Apr 24 09:06 _vti_bin
drwxr-x---  6 alfresco Alfresco         9 Apr 24 09:06 ROOT
-rwxrwxr-x  1 root     Alfresco 108411684 Apr 24 09:06 share.war
-rwxrwxr-x  1 root     Alfresco 134742228 Apr 24 09:06 alfresco.war
-rwxrwxr-x  1 root     Alfresco   2357157 Apr 24 09:06 _vti_bin.war
-rwxrwxr-x  1 root     Alfresco    274777 Apr 24 09:06 ROOT.war

```
### Clean `tomcat/work/Catalina/localhost` 

```shell
# check
$ sudo ls -lt /usr/local/alfresco-community23x/tomcat/work/Catalina/localhost/
total 36
drwxr-x--- 2 alfresco Alfresco 2 Apr 24 09:06 _vti_bin
drwxr-x--- 2 alfresco Alfresco 2 Apr 24 09:06 ROOT
drwxr-x--- 2 alfresco Alfresco 2 Apr 24 08:20 alfresco
drwxr-x--- 2 alfresco Alfresco 2 Apr 24 08:20 share

# delete
$ sudo rm -rf /usr/local/alfresco-community23x/tomcat/work/Catalina/localhost/*

# update permissions
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/tomcat/work/Catalina/localhost
$ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/work/Catalina/localhost
```

###  Edit `server.xml`
**/usr/local/alfresco-community23x/tomcat/conf/server.xml:**

* Find Connector with port "8080”.
* Add the `URIEncoding` and `maxHttpHeaderSize` attributes.
* Add the `enableLookups`, `xpoweredBy` and `server` attributes. (Optional Attributes for Tomcat 8.x and later)

   * Set `xpoweredBy` attribute to `true` to cause Tomcat to advertise support for the Servlet specification using the header recommended in the specification. The default value is false.
   * Set `enableLookups` to `true` if you want calls to request.getRemoteHost() to perform DNS lookups in order to return the actual host name of the remote client. Set to false to skip the DNS lookup and return the IP address in String form instead (thereby improving performance). By default, DNS lookups are disabled.
   * Setting `server` to `true`, overrides the server header for the http response. If set, the value for this attribute overrides any Server header set by a web application. If not set, any value specified by the application is used. If the application does not specify a value then no Server header is set.

```xml
<Connector port="8080" protocol="HTTP/1.1"
   URIEncoding="UTF-8" 
   connectionTimeout="20000"
   maxHttpHeaderSize="32768"
   redirectPort="8443" 
   enableLookups="false" 
   xpoweredBy="false" 
   server="AlfrescoECM"/>
```
* `URIEncoding`: Tomcat uses **ISO-8859-1** character encoding when decoding URLs that are received from a browser. *This can cause problems when creating, uploading, and renaming files with international characters.*

* `maxHttpHeaderSize`: Tomcat uses an 8 KB header buffer size, which might not be large enough for the Kerberos authentication protocol. We need to increase this buffer size.



### Edit `catalina.properties`
**/usr/local/alfresco-community23x/tomcat/conf/catalina.properties**

Update the value of the `shared.loader=`

```
shared.loader=${catalina.base}/shared/classes,${catalina.base}/shared/lib/*.jar
```

### Edit `catalina.sh`

 Add `JAVA_TOOL_OPTIONS` java environment variable for metadata encryption. *Add the variable after `JAVA_OPTS` in catalina.sh file*. If you add this variable at the end of the file, it is not getting recognized for some reason.

 **/usr/local/alfresco-community23x/tomcat/bin/catalina.sh**

 ```
 ...

if [ -z "$JSSE_OPTS" ] ; then
  JSSE_OPTS="-Djdk.tls.ephemeralDHKeySize=2048"
fi
JAVA_OPTS="$JAVA_OPTS $JSSE_OPTS"

# Register custom URL handlers
# Do this here so custom URL handles (specifically 'war:...') can be used in the security policy
JAVA_OPTS="$JAVA_OPTS -Djava.protocol.handler.pkgs=org.apache.catalina.webresources"

# Customize add for :  Metadata Encryption
#
# ACS23x Custom changes [Start] ##############

export JAVA_TOOL_OPTIONS="-Dencryption.keystore.type=JCEKS -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding -Dencryption.keyAlgorithm=DESede -Dencryption.keystore.location=/usr/local/alfresco-community23x/tomcat/shared/classes/alfresco/extension/keystore/metadata-keystore/keystore -Dmetadata-keystore.password=mp6yc0UD9e -Dmetadata-keystore.aliases=metadata -Dmetadata-keystore.metadata.password=oKIWzVdEdA -Dmetadata-keystore.metadata.algorithm=DESede"

# ACS23x Custom changes [End] ##############

```


### Edit `alfresco-global.properties`

**/usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties**

```shell
# Check
$ ls /usr/local/alfresco-community23x/tomcat/shared/classes/classes/
alfresco  alfresco-global.properties.sample


# copy
$ sudo cp /usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties.sample /usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties

# Update the file permissions

$ sudo chgrp Alfresco /usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties
$ sudo chmod 775 /usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties

```

* Add the “dir.root” property 
   ```
   dir.root=/usr/local/alfresco-community70/alf_data
   dir.keystore=/usr/local/alfresco-community70/tomcat/shared/classes/alfresco/extension/keystore
   ```

* Add alfresco and share host, port, context and protocol specific properties
   ```
   alfresco.context=alfresco
   alfresco.host=${localname}
   alfresco.port=8080
   alfresco.protocol=http
   share.context=share
   share.host=${localname}
   share.port=8080
   share.protocol=http
   ```
* Add the database connection properties
   ```
   db.driver=org.postgresql.Driver
   db.username=alfresco
   db.password=alfresco
   db.name=alfresco
   db.url=jdbc:postgresql://localhost:5432/${db.name}
   db.pool.max=275
   db.pool.validate.query=SELECT 1
   ```

* Add the server mode property, leave it default to ‘UNKNOWN’.
   ```
   system.serverMode=UNKNOWN
   ```

* Add the alfresco rmi services port and host properties.
   ```
   alfresco.rmi.services.port=50500

   alfresco.rmi.services.host=0.0.0.0
   ```


### Reference
* [Configure Tomcat for ACS](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#configure-tomcat-acs-repo)