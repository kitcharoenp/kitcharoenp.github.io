---
layout : post
title : "Alfresco Installation in Ubuntu Using ZIP"
categories : [alfresco]
published : false
---

### [Alfresco installation in Ubuntu using ZIP Distribution Files](https://github.com/aborroy/alfresco-ubuntu-installer/tree/main)


#### 1.PostgreSQL Installation
Installs and configures PostgreSQL, to be used as object-relational database system.

**Script:** `01-install_postgres.sh`
```

```

#### 2.Java Installation
Installs Java Development Kit (JDK), essential for running Apache Tomcat and Java applications like Apache Solr, Apache ActiveMQ and Transform Service.

**Script:** `02-install_java.sh`

```shell
$ sudo ./02-install_java.sh
...
...
Checking Java version...
openjdk version "17.0.14" 2025-01-21
...
Java JDK 17 installation and setup completed successfully!
```

#### 3.Tomcat Installation
Installs Apache ActiveMQ, to be used as messaging server.

**Script:** `03-install_tomcat.sh`

```shell
$ sudo ./03-install_tomcat.sh
Using Tomcat version: 10.1.40
Updating package list..
...
Apache Tomcat installation and setup completed successfully!
```

#### 4.ActiveMQ Installation
Installs Apache ActiveMQ, to be used as messaging server.

**Script:** `04-install_activemq.sh`

```shell
$ sudo ./04-install_activemq.sh
Installing Curl...
Reading package lists... Done
...
Reloading systemd daemon...
Starting ActiveMQ service...
Apache ActiveMQ installation and setup completed successfully!
```

#### 5.Alfresco Resources Download
Downloads necessary resources for Alfresco, including web applications, search service and transform service.

**Script:** `05-download_alfresco_resources.sh`

```shell
$ sudo ./05-download_alfresco_resources.sh
Downloading alfresco-content..
...
HTTP Status: 200
Downloaded alfresco-transform-core-aio-5.1.7.jar successfully.
All downloads are complete.
```


#### 6.Alfresco Installation
Installs Alfresco Community Edition, configuring Alfresco and Share web applications.

**Script:** `06-install_alfresco.sh`

```shell
$ sudo ./06-install_alfresco.sh
...
Alfresco has been configured
```

#### 7.Solr Installation
Installs Apache Solr, to be used as search platform for indexing and searching data.

**Script:** `07-install_solr.sh`

```shell
$ sudo ./07-install_solr.sh
...
SOLR has been configured
```

**Script:** `07-install_solr.sh`

#### 8.Transform Service Installation
Installs services required for document transformations within Alfresco.

**Script:** `08-install_transform.sh`

```shell
$ sudo ./08-install_transform.sh
...
Configure Transform server
Creating Transform systemd service file...
[Unit]
Description=Transform Application Container
After=network.target activemq.service
Requires=activemq.service

[Service]
Type=simple

User=ubuntu
Group=ubuntu

Environment="JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64"
Environment="LIBREOFFICE_HOME=/usr/lib/libreoffice"
############################################ ADJUST THE LINE BELOW TO REFLECT THE CORRECT JAR FILE ##########################################
ExecStart=java -jar /home/ubuntu/transform/alfresco-transform-core-aio-5.1.7.jar
ExecStop=/bin/kill -15 
...

Transform has been configured
```

#### 9.Alfresco Content App Building
Builds static website from NodeJS application ACA. This task can be performed in a separate server or machine.

**Script:** `09-build_aca.sh`

```shell
$ sudo ./09-build_aca.sh
Installing Node.js and npm...
2025-04-30 07:11:41 - Installing pre-requisites
...

>  NX   Successfully ran target build for project content-ce and 2 tasks it depends on (3m)
```

#### 10.Nginx Installation
Installs web server for ACA and configure web proxy for Alfresco and Share web applications.

**Script:** `10-install_nginx.sh`

```shell
$ sudo ./10-install_nginx.sh
Updating system...
...
The following NEW packages will be installed:
  nginx nginx-common
0 upgraded, 2 newly installed, 0 to remove and 3 not upgraded.
...
Creating directory for Alfresco Content App...
cp: cannot stat '/home/ubuntu/alfresco-content-app/dist/content-ce/*': No such file or directory

```

#### 11.Start Services
Starts all the installed services to ensure they are running correctly.


**Script:** `11-start_services.sh`

Although the `11-start_services.sh` script includes the sequence for executing the services,**it is recommended to run each line manually**
```shell
$ sudo ./11-start_services.sh
...


### Manually Run Services

**postgresql:**

```shell
$ sudo systemctl start postgresql
$ sudo systemctl status postgresql
● postgresql.service - PostgreSQL RDBMS
   Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; preset: enabled)
   Active: active (exited) since Wed 2025-04-30 04:30:55 UTC; 2h 56min ago
```

**activemq:**
```shell
$ sudo systemctl start activemq
$ sudo systemctl status activemq
● activemq.service - Apache ActiveMQ
   Loaded: loaded (/etc/systemd/system/activemq.service; enabled; preset: enabled)
   Active: active (running) since Wed 2025-04-30 07:06:47 UTC; 30min ago
   ...
   CGroup: /system.slice/activemq.service
             └─16525 /usr/lib/jvm/java-17-openjdk-amd64/bin/java -Xms64M -Xmx1G -Djava.util.logging.config.file=logging.properties -Djava.sec>
```

**transform:**
```shell
$ sudo systemctl start transform
$ sudo systemctl status transform
● transform.service - Transform Application Container
   Loaded: loaded (/etc/systemd/system/transform.service; enabled; preset: enabled)
   Active: active (running) since Wed 2025-04-30 07:44:20 UTC; 6s ago
   CGroup: /system.slice/transform.service
            └─18876 java -jar /home/ubuntu/transform/alfresco-transform-core-aio-5.1.7.jar
...
   Apr 30 07:44:20 alfresco2302 systemd[1]: Started transform.service - Transform Application C>
   Apr 30 07:44:23 alfresco2302 java[18876]:   .   ____          _            __ _ _
   Apr 30 07:44:23 alfresco2302 java[18876]:  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
   Apr 30 07:44:23 alfresco2302 java[18876]: ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
   Apr 30 07:44:23 alfresco2302 java[18876]:  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
   Apr 30 07:44:23 alfresco2302 java[18876]:   '  |____| .__|_| |_|_| |_\__, | / / / /
   Apr 30 07:44:23 alfresco2302 java[18876]:  =========|_|==============|___/=/_/_/_/
   Apr 30 07:44:23 alfresco2302 java[18876]:  :: Spring Boot ::                (v3.4.3)
   Apr 30 07:44:24 alfresco2302 java[18876]: 2025-04-30T07:44:24.067Z  INFO 18876 --- [ 

```

**tomcat:**
```shell
$ sudo systemctl start tomcat
$ sudo systemctl status tomcat
● tomcat.service - Apache Tomcat Web Application Container
   Loaded: loaded (/etc/systemd/system/tomcat.service; enabled; preset: enabled)
   Active: active (running) since Wed 2025-04-30 07:46:43 UTC; 11s ago
   ...
   CGroup: /system.slice/tomcat.service
            └─19002 /usr/lib/jvm/java-17-openjdk-amd64/bin/java -Djava.util.logging.config.>

# Check
tail -f /home/ubuntu/tomcat/logs/catalina.out
...
30-Apr-2025 07:47:31.877 INFO [main] org.apache.catalina.startup.Catalina.start Server startup in [46423] milliseconds
```

**solr:**
```shell
# Start
$ sudo systemctl start solr
Job for solr.service failed because the control process exited with error code.
See "systemctl status solr.service" and "journalctl -xeu solr.service" for details.

# Status
$ systemctl status solr.service
× solr.service - Apache SOLR Web Application Container
     Loaded: loaded (/etc/systemd/system/solr.service; enabled; preset: enabled)
     Active: failed (Result: exit-code) since Wed 2025-04-30 07:51:38 UTC; 1min 21s ago
    Process: 19087 ExecStart=/home/ubuntu/alfresco-search-services/solr/bin/solr start -a -Dcreate.alfresco.defaults=alfresco,archive -Dalfre>
...
Apr 30 07:51:37 alfresco2302 solr[19087]: Failed archiving old console logs
Apr 30 07:51:38 alfresco2302 solr[19087]: ERROR: Logs directory ../../logs is not writable. Exiting
Apr 30 07:51:38 alfresco2302 systemd[1]: solr.service: Control process exited, code=exited, status=1/FAILURE
Apr 30 07:51:38 alfresco2302 systemd[1]: solr.service: Failed with result 'exit-code'.
Apr 30 07:51:38 alfresco2302 systemd[1]: Failed to start solr.service - Apache SOLR Web Application Container.
Apr 30 07:51:38 alfresco2302 systemd[1]: solr.service: Consumed 4.191s CPU time.
```

The error "Logs directory ../../logs is not writable" in Solr on Ubuntu means the Solr service is unable to write logs to the specified directory. This is usually due to incorrect permissions. To fix this, you need to ensure the user Solr runs as has write permissions to the logs directory, typically /var/solr/logs. 


### Here's a step-by-step solution:

1. Identify the Solr user: Determine which user Solr runs as. This is often solr or dms. 

2. Check permissions: Use the ls -ld /var/solr/logs command to verify the permissions of the logs directory. It should have write permissions for the Solr user (e.g., drwxr-xr-x 2 solr solr ...).
3. Adjust permissions if needed: If the permissions are not correct, use sudo chown solr:solr /var/solr/logs to change the owner and group to the Solr user. You might also need to use sudo chmod 775 /var/solr/logs to grant the necessary write permissions. 
4. Restart Solr: After adjusting the permissions, restart the Solr service to apply the changes. 
5. Verify: Check the Solr logs to confirm that the service is now able to write logs.

### Here's a more detailed explanation of each step: 

   #### Identifying the Solr User:
    The Solr user can be configured in the solr.in.sh file or in the Solr installation script. Look for a setting like SOLR_USER.
   #### Checking Permissions:
    The ls -ld /var/solr/logs command will show you the directory's permissions. The drwxr-xr-x part indicates that the directory is a directory, and the permissions are:
        * rwx: read, write, execute permissions for the owner (Solr user)
        * r-x: read, execute permissions for the group
        * r-x: read, execute permissions for others 
   #### Adjusting Permissions:
        * sudo chown solr:solr /var/solr/logs changes the ownership of the logs directory to the Solr user and group.
        * sudo chmod 775 /var/solr/logs grants read, write, and execute permissions for the owner and group, and read and execute permissions for others. Using chmod 777 is generally not recommended for security reasons. 
   #### Restarting Solr:
    The specific command to restart Solr depends on how it's installed (e.g., as a systemd service). You can usually use commands like sudo systemctl restart solr or sudo /etc/init.d/solr restart.
   #### Verifying:
    Check the /var/solr/logs/solr.log file (or the appropriate log file based on your Solr configuration) to see if Solr is writing logs. 

By following these steps, you should be able to resolve the "Logs directory not writable" error and ensure that Solr can write its logs correctly


**nginx:**
```shell
$ sudo systemctl start nginx
$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
   Active: active (running) since Wed 2025-04-30 07:20:05 UTC; 6 days ago
      Docs: man:nginx(8)
   Process: 18580 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=>
   Process: 18581 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
```

### Troubleshooting

#### Failed to create store
**/home/ubuntu/tomcat/logs/alfresco.log**
```
03300000 Failed to create store root: /home/ubuntu/alf_data/contentstore.delete

# Fixed
sudo chown -R ubuntu:ubuntu /home/ubuntu/alf_data/
```