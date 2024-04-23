---
layout : post
title : "Alfresco Community 23.x Install Tomcat"
categories : [alfreco]
published : true
---
### Alfresco Supported platforms
[Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Downloaded the distribution packages
Navigate to the Apache [Tomcat official download page](http://tomcat.apache.org/download-10.cgi), and grab the latest version of the binary distribution in tar.gz format.

**v10.1.20:**
```
$ wget https://downloads.apache.org/tomcat/tomcat-10/v10.1.20/bin/apache-tomcat-10.1.20.tar.gz
```

### Extract the package 

### Update directory permissions

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
   ```

### Reference

*  [Install Tomcat](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#install-tomcat-for-acs)

* [How to Install and Configure Tomcat 10 on Ubuntu 22.04](https://tecadmin.net/how-to-install-tomcat-on-ubuntu-22-04/)