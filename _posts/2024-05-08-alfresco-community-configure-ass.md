---
layout : post
title : "Alfresco Community 23.x Setup and Configure ASS"
categories : [alfreco]
published : true
---

### Unzip
Unzip the `alfresco-search-services-2.0.1.zip` package.

```shell
$ cd /home/ubuntu/
# unzip
$ sudo unzip alfresco-search-services-2.0.9.zip -d alfresco-search-services-2.0.9
# list
$ ls -l alfresco-search-services-2.0.9/alfresco-search-services/
total 59
-rw-r--r-- 1 root root 1328 Jan 15 11:03 README.MD
drwxr-xr-x 4 root root    7 Jan 15 11:04 licenses
drwxr-xr-x 2 root root    4 Jan 15 11:03 logs
drwxr-xr-x 6 root root   11 Jan 15 11:04 solr
-rw-r--r-- 1 root root 6880 Jan 15 11:04 solr.in.cmd
-rw-r--r-- 1 root root 7358 Jan 15 11:04 solr.in.sh
drwxr-xr-x 5 root root    7 Jan 15 11:03 solrhome
```

### Copy
Copy the extracted folder `alfresco-search-services` to `/usr/local/alfresco-search-services/`

```shell
# copy
$ sudo cp -R /home/ubuntu/alfresco-search-services-2.0.9/alfresco-search-services/* /usr/local/alfresco-search-services/

# list
$ ls -l /usr/local/alfresco-search-services/
total 59
-rw-r--r-- 1 root root 1328 May  8 03:52 README.MD
drwxr-xr-x 4 root root    7 May  8 03:52 licenses
drwxr-xr-x 2 root root    4 May  8 03:52 logs
drwxr-xr-x 6 root root   11 May  8 03:52 solr
-rw-r--r-- 1 root root 6880 May  8 03:52 solr.in.cmd
-rw-r--r-- 1 root root 7358 May  8 03:52 solr.in.sh
drwxr-xr-x 5 root root    7 May  8 03:52 solrhome
```

### Update
Update the permissions

```shell
# Change owner
$ sudo chown -R solr:Solr /usr/local/alfresco-search-services

# Update permissions
$ sudo chmod -R 755 /usr/local/alfresco-search-services
$ sudo chmod -R 775 /usr/local/alfresco-search-services/logs
$ sudo chmod -R 775 /usr/local/alfresco-search-services/solrhome

```

### `alfresco-global.properties`

```shell
$ sudo vim /usr/local/alfresco-community23x/tomcat/shared/classes/classes/alfresco-global.properties
```

Add the following configuration properties:

```
solr.host=localhost
solr.port=8983

#secret, https -- For ACS7.2 onwards
solr.secureComms=secret
solr.sharedSecret=secret # A password/secret string, here I am keeping it as 'secret'

solr.base.url=/solr
index.subsystem.name=solr6
```



### [Install without mutual TLS](https://docs.alfresco.com/search-services/latest/install/options/#install-without-mutual-tls-http-with-secret-word-in-request-header) on `solr.in.sh`

```shell
$ sudo vim /usr/local/alfresco-search-services/solr.in.sh
```

```
SOLR_SOLR_HOST=localhost
SOLR_SOLR_PORT=8983
SOLR_SOLR_BASEURL=/solr
SOLR_ALFRESCO_HOST=localhost
SOLR_ALFRESCO_PORT=8080
SOLR_ALFRESCO_BASEURL=/alfresco

# Since we are setting up with no SSL, this property need to be set to secret. 
# Default is https. For ACS7.2 onwards
# ACS72 Shared secret 
# [Start] ##############
export JAVA_TOOL_OPTIONS="-Dalfresco.secureComms.secret=secret"
############ OR Instead of JAVA_TOOL_OPTIONS, you can also export SOLR_OPTS , 
#example below. Make sure you dont export both###

#export SOLR_OPTS="-Dalfresco.secureComms.secret=secret"

# ACS72 Shared secret [End] ##############
SOLR_ALFRESCO_SECURECOMMS=secret
```

* [Trouble with Solr does not communicate with Alfresco secureComms=secret](https://hub.alfresco.com/t5/alfresco-content-services-forum/trouble-with-solr-does-not-communicate-with-alfresco-securecomms/td-p/313577)

> "Missing value for alfresco.secureComms.secret configuration property. Make sure to pass this property as a JVM Argument (eg. -Dalfresco.secureComms.secret=my-secret-value)."


### Set `alfresco.secureComms`
Set `alfresco.secureComms=secret`
```shell
$ sudo vim /usr/local/alfresco-search-services/solrhome/templates/rerank/conf/solrcore.properties
$ sudo vim /usr/local/alfresco-search-services/solrhome/alfresco/conf/solrcore.properties
$ sudo vim /usr/local/alfresco-search-services/solrhome/archive/conf/solrcore.properties
```


### Starting alfresco search service 

```shell
# start service
$ sudo -u solr /usr/local/alfresco-search-services/solr/bin/solr start -a "-Dcreate.alfresco.defaults=alfresco,archive"

# output
Warning: Available entropy is low. As a result, use of the UUIDField, SSL, or any other features that require
RNG might not work properly. To check for the amount of available entropy, use 'cat /proc/sys/kernel/random/entropy_avail'.

Waiting up to 180 seconds to see Solr running on port 8983 [|]  
Started Solr server on port 8983 (pid=1773). Happy searching!

```
Note that, `-Dcreate.alfresco.defaults=alfresco,archive` command automatically creates the alfresco and archive cores. So you need to pass this param **only on first/initial startup of solr** in order to allow cores being created and configured.


### Stopping alfresco search service
```shell
# stop service
$ sudo -u solr /usr/local/alfresco-search-services/solr/bin/solr stop -p 8983

# Or

$ sudo -u solr /usr/local/alfresco-search-services/solr/bin/solr stop -all

# output
find: Failed to restore initial working directory: /home/ubuntu: Permission denied
Sending stop command to Solr running on port 8983 ... waiting up to 180 seconds to allow Jetty process 1773 to stop gracefully.
```

### Configure solr log
The logs are stored in the `/usr/local/alfresco-search-services/logs/solr.log` file, by default. This can be configured in `solr.in.sh` using **SOLR_LOGS_DIR**.

```
# Default settings

# Location where Solr should write logs to. Absolute or relative to solr start dir
SOLR_LOGS_DIR=../../logs
LOG4J_PROPS=$SOLR_LOGS_DIR/log4j.properties
```

### Create a system service

```shell
$ sudo vim /etc/systemd/system/solr.service
```

**/etc/systemd/system/solr.service**
```
# Systemd unit file for solr6
[Unit]
Description=Alfresco search service
After=syslog.target network.target

[Service]
Type=forking
Restart=always

Environment=JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

ExecStart=/usr/local/alfresco-search-services/solr/bin/solr start
ExecStop=/usr/local/alfresco-search-services/solr/bin/solr stop -all

User=solr
Group=Solr

[Install]
WantedBy=multi-user.target
```

```shell
# Reload demons
$ sudo systemctl daemon-reload

# Start 
$ sudo systemctl start solr

# Enable solr to automatically start at boot time
$ sudo systemctl enable solr
# Output
Created symlink /etc/systemd/system/multi-user.target.wants/solr.service → /etc/systemd/system/solr.service.

# Check status 
$ sudo systemctl status solr

# Output
● solr.service - Alfresco search service
     Loaded: loaded (/etc/systemd/system/solr.service; disabled; vendor preset: enabled)
     Active: active (running) since Wed 2024-05-08 09:58:54 UTC; 50s ago
    Process: 3449 ExecStart=/usr/local/alfresco-search-services/solr/bin/solr start (code=exited, status=0/SUCCESS)
   Main PID: 3592 (java)
      Tasks: 102 (limit: 37387)
     Memory: 1.2G
        CPU: 18.183s
     CGroup: /system.slice/solr.service
             └─3592 /usr/lib/jvm/java-17-openjdk-amd64/bin/java -server -Xms1g -Xmx1g -XX:+UseG1GC -XX:+PerfDisableSharedMem -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=250 -XX:+UseLargePages -XX:+A>

May 08 09:58:50 alfresco23x systemd[1]: Starting Alfresco search service...
May 08 09:58:51 alfresco23x solr[3449]: Warning: Available entropy is low. As a result, use of the UUIDField, SSL, or any other features that require
May 08 09:58:51 alfresco23x solr[3449]: RNG might not work properly. To check for the amount of available entropy, use 'cat /proc/sys/kernel/random/entropy_avail'.
May 08 09:58:54 alfresco23x solr[3449]: [110B blob data]
May 08 09:58:54 alfresco23x solr[3594]: Started Solr server on port 8983 (pid=3592). Happy searching!
May 08 09:58:54 alfresco23x solr[3449]: [14B blob data]
May 08 09:58:54 alfresco23x systemd[1]: Started Alfresco search service.
lines 1-18/18 (END)
```


### [Schema auto-update failed](https://hub.alfresco.com/t5/alfresco-content-services-forum/schema-auto-update-failed/td-p/219277)


### [Disable Transform Service](https://docs.alfresco.com/transform-service/latest/config/)

**alfresco-global.properties:**
```
transform.service.enabled=true
local.transform.service.enabled=true
```


* 2024-05-10 14:22:46,774 ERROR [org.springframework.web.context.ContextLoader] [main] Context initialization failed
org.alfresco.error.AlfrescoRuntimeException: 04100039 Keystores are invalid



### See Log Error
**/usr/local/alfresco-search-services/logs/solr.log**
```shell
$ sudo cat /usr/local/alfresco-search-services/logs/solr.log | grep ERROR
2024-05-09 08:08:38.420 ERROR (searcherExecutor-8-thread-1-processing-x:alfresco) [   x:alfresco] o.a.s.c.SolrCore null:java.lang.IllegalArgumentException: Invalid communications type
2024-05-09 08:08:38.420 ERROR (searcherExecutor-7-thread-1-processing-x:archive) [   x:archive] o.a.s.c.SolrCore null:java.lang.IllegalArgumentException: Invalid communications type

```

> To finish, i had alfresco.log and share.log error on catalina.out
i had to change those files

```
cd /usr/local/alfresco-community70/
grep -R 'log4j.appender.File.File=' ./
./tomcat/webapps/share/WEB-INF/classes/log4j.properties:log4j.appender.File.File=share.log
./tomcat/webapps/alfresco/WEB-INF/classes/log4j.properties:log4j.appender.File.File=alfresco.log
and i changed the log4j.appender.File.File on both file to these
./tomcat/webapps/share/WEB-INF/classes/log4j.properties:log4j.appender.File.File=logs/share.log
./tomcat/webapps/alfresco/WEB-INF/classes/log4j.properties:log4j.appender.File.File=logs/alfresco.log
```

```
./share/WEB-INF/classes/log4j2.properties:### ERROR Unable to create file share.log java.io.IOException: Permission denied
./share/WEB-INF/classes/log4j2.properties:appender.rolling.fileName=logs/share.log # default: share.log 
```


```
./alfresco/WEB-INF/classes/log4j2.properties:appender.rolling.fileName=alfresco.log
```



```
dir.keystore=${dir.root}/keystore
```

org.postgresql.util.PSQLException: ERROR: relation "alf_prop_class" does not exist

### Reference
* [Setup and Configure ASS](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-and-configure-ass)

* [Alfresco Search Services 2.0 Installation options](https://docs.alfresco.com/search-services/latest/install/options/#install-without-mutual-tls-http-with-secret-word-in-request-header)

* [Alfresco Community Edition "Cannot find Alfresco Repository on this server"](https://stackoverflow.com/questions/56712582/alfresco-community-edition-cannot-find-alfresco-repository-on-this-server)

* https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html?showComment=1630537894717#c909321641820170862