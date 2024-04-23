---
layout : post
title : "Alfresco Community 23.x Install Tomcat"
categories : [alfreco]
published : true
---
### Alfresco Supported platforms
> [Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Downloaded the distribution packages
Navigate to the Apache [Tomcat official download page](http://tomcat.apache.org/download-10.cgi), and grab the latest version of the binary distribution in tar.gz format.

**v10.1.20:**
```
$ wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.20/bin/apache-tomcat-10.1.20.tar.gz
```

### Extract the package 

```shell
# extract
$ sudo tar -xvf /home/ubuntu/apache-tomcat-10.1.20-bin.tar.gz

# copy
$ sudo cp -R /home/ubuntu/apache-tomcat-10.1.20/* /usr/local/alfresco-community23x/tomcat/
```

### Update directory permissions

```shell
    $ sudo chgrp -R Alfresco /usr/local/alfresco-community23x
         
    $ sudo chmod -R 755 /usr/local/alfresco-community23x/tomcat/bin
         
    $ sudo chmod -R 755 /usr/local/alfresco-community23x/tomcat/conf
      
    $ sudo chmod -R 755 /usr/local/alfresco-community23x/tomcat/shared

    $ sudo chmod -R 755 /usr/local/alfresco-community23x/tomcat/lib

    $ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/temp
         
    $ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/logs

    $ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/work
         
    $ sudo chmod -R 775 /usr/local/alfresco-community23x/tomcat/webapps
   ```

### Create a system service

* Create a `tomcat.service`

   **/etc/systemd/system/tomcat.service:**

   ```console
   # Systemd unit file for tomcat
   [Unit]
   Description=Apache Tomcat Web Application Container
   After=syslog.target network.target

   [Service]
   Type=forking
   Restart=always


   # JAVA_HOME depend on os see more on Alfresco Community 23.x Install Java 
   Environment=JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
   Environment=CATALINA_PID=/usr/local/alfresco-community23x/tomcat/temp/tomcat.pid
   Environment=CATALINA_HOME=/usr/local/alfresco-community23x/tomcat
   Environment=CATALINA_BASE=/usr/local/alfresco-community23x/tomcat
   Environment='CATALINA_OPTS=-Xms3G -Xmx4G -Xss1024k -server -XX:+UseParallelGC'
   Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

   ExecStart=/usr/local/alfresco-community23x/tomcat/bin/startup.sh
   ExecStop=/bin/kill -15 $MAINPID
   WorkingDirectory=/usr/local/alfresco-community23x/tomcat
   User=alfresco
   Group=Alfresco

   [Install]
   WantedBy=multi-user.target
   ```

* Reload demons
   ```shell
   $ sudo systemctl daemon-reload
   ```

* Start and enable `Tomcat` at boot time
   ```shell
   # start
   $ sudo systemctl start tomcat

   # enable
   $ sudo systemctl enable tomcat

   # check status
   $ sudo systemctl status tomcat

   # output
   ● tomcat.service - Apache Tomcat Web Application Container
     Loaded: loaded (/etc/systemd/system/tomcat.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-04-23 09:30:51 UTC; 17s ago
   Main PID: 3999 (java)
      Tasks: 31 (limit: 37387)
     Memory: 151.4M
        CPU: 5.799s
     CGroup: /system.slice/tomcat.service

   ```

### Reference

*  [Install Tomcat](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#install-tomcat-for-acs)

* [How to Install and Configure Tomcat 10 on Ubuntu 22.04](https://tecadmin.net/how-to-install-tomcat-on-ubuntu-22-04/)