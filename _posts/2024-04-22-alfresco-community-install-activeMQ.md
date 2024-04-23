---
layout : post
title : "Alfresco Community 23.x Install ActiveMQ v5.18"
categories : [alfreco]
published : true
---
### Alfresco Supported platforms
> [Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Download
Download the **ActiveMQ** `v5.18` from its [official download page](https://activemq.apache.org/components/classic/download/)

```shell
# download
$ wget https://downloads.apache.org//activemq/5.18.4/apache-activemq-5.18.4-bin.tar.gz
```

### Extract the package

```shell
# extract
$ sudo tar -xvf /home/ubuntu/apache-activemq-5.18.4-bin.tar.gz

# copy
$ sudo cp -R /home/ubuntu/apache-activemq-5.18.4/* /usr/local/alfresco-community23x/activemq/
```


### Create a system service

* Create a `activemq.service`

   **/etc/systemd/system/activemq.service:**

   ```console
   [Unit]
   Description=ActiveMQ service
   After=network.target

   [Service]
   Type=forking
   ExecStart=/usr/local/alfresco-community23x/activemq/bin/activemq start
   ExecStop=/usr/local/alfresco-community23x/activemq/bin/activemq stop
   User=alfresco
   Group=Alfresco
   WorkingDirectory=/usr/local/alfresco-community23x/activemq/data
   Restart=always
   RestartSec=9
   StandardOutput=syslog
   StandardError=syslog
   SyslogIdentifier=activemq

   [Install]
   WantedBy=multi-user.target
   ```

* Reload demons
   ```shell
   $ sudo systemctl daemon-reload
   ```
* Change the permission

   ```shell
   # change group
   $ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/activemq

   # change owner 
   $ sudo chown -R alfresco:Alfresco /usr/local/alfresco-community23x/activemq/data
   ```

* Start and enable `ActiveMQ` at boot time
   ```shell
   # start
   $ sudo systemctl start activemq

   # enable
   $ sudo systemctl enable activemq

   # check status
   $ sudo systemctl status activemq

   # output
   ● activemq.service - ActiveMQ Service
     Loaded: loaded (/etc/systemd/system/activemq.service; disabled; vendor preset: enabled)
     Active: active (running) since Tue 2024-04-23 07:32:58 UTC; 1min 36s ago
     ...
   ```

   **output:** `/usr/local/alfresco-community23x/activemq/data/activemq.log`
   ```
   ...
   2024-04-23 07:49:02,715 | INFO  | Connector ws started | org.apache.activemq.broker.TransportConnector | main
   2024-04-23 07:49:02,715 | INFO  | Apache ActiveMQ 5.18.4 (localhost, ID:alfresco23x-37585-1713858542429-0:1) started | org.apache.activemq.broker.BrokerService | main
   2024-04-23 07:49:02,715 | INFO  | For help or more information please see: http://activemq.apache.org | org.apache.activemq.broker.BrokerService | main
   2024-04-23 07:49:03,244 | INFO  | ActiveMQ WebConsole available at http://127.0.0.1:8161/ | org.apache.activemq.web.WebConsoleStarter | main
   2024-04-23 07:49:03,244 | INFO  | ActiveMQ Jolokia REST API available at http://127.0.0.1:8161/api/jolokia/ | org.apache.activemq.web.WebConsoleStarter | main
   ```

### Test the web console


* Enable ActiveMQ access for a local or public network

   The default `ActiveMQ` allows on localhost only. To enable ActiveMQ access for a local or public network, edit `conf/jetty.xml` configuration file.

   ```shell
   $ sudo vim /usr/local/alfresco-community23x/activemq/conf/jetty.xml
   ```
   
   Search for the below configuration section.

   ```xml
       <bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
             <!-- the default port number for the web console -->
        <property name="host" value="127.0.0.1"/>
        <property name="port" value="8161"/>
      </bean>
   ```

   Change host value from `"127.0.0.1"`to system IP address or set `0.0.0.0` to listen on all interfaces.

* Test the web console: `http://server-ip:8161/`. Default `user` name and `password` is : **admin/admin**

   ![Apache ActiveMQ Web Console ](/assets/img/2024/Screenshot%20from%202024-04-23%2015-28-05.png)

   ![Manage ActiveMQ broker](/assets/img/2024/Screenshot%20from%202024-04-23%2015-28-15.png)

### Error

* temp dir not configured correctly: writeable=false

   ```log
   java.lang.IllegalStateException: Parent for temp dir not configured correctly: writeable=false
   ```
   **Solution:**

   > One of the directory named `tmp` is created by `activemq` at run time may be **causing permission issues since service is started** as `alfresco` user and not the `root` user, so we will create `tmp` directory before hand and put appropriate permissions for the safe side

   ```shell
   $ sudo mkdir /usr/local/alfresco-community23x/activemq/tmp
   $ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/activemq/tmp
   $ sudo chmod -R 775 /usr/local/alfresco-community23x/activemq/tmp
   ```


### Reference

*  [Install ActiveMQ](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#install-activemq-for-transformation)

* [How to Install Apache ActiveMQ on Ubuntu 22.04](https://tecadmin.net/how-to-install-apache-activemq-on-ubuntu-22-04/)