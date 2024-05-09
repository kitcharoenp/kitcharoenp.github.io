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

### [Installation options](https://docs.alfresco.com/search-services/latest/install/options/#install-without-mutual-tls-http-with-secret-word-in-request-header) on `solr.in.sh`

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
#export JAVA_TOOL_OPTIONS="-Dalfresco.secureComms.secret=secret"
############ OR Instead of JAVA_TOOL_OPTIONS, you can also export SOLR_OPTS , 
#example below. Make sure you dont export both###

export SOLR_OPTS="-Dalfresco.secureComms.secret=secret"

# ACS72 Shared secret [End] ##############
SOLR_ALFRESCO_SECURECOMMS=secret
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

# Start and enable solr to automatically start at boot time
$ sudo systemctl start solr

$ sudo systemctl enable solr

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


### Reference
* [Setup and Configure ASS](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-and-configure-ass)

* [Alfresco Search Services 2.0 Installation options](https://docs.alfresco.com/search-services/latest/install/options/#install-without-mutual-tls-http-with-secret-word-in-request-header)