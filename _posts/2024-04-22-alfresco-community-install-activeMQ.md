---
layout : post
title : "Alfresco Community 23.x Install ActiveMQ v5.18"
categories : [alfreco]
published : true
---
### Alfresco Supported platforms
[Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Download
Download the ActiveMQ v5.18 from its [official download page](https://activemq.apache.org/components/classic/download/)

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
   WorkingDirectory=~/usr/local/alfresco-community23x/activemq/data
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
   ```


### Test the web console

### Reference

*  [Install ActiveMQ](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#install-activemq-for-transformation)