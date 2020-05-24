---
layout: post
title: "Alfresco : Installing Alfresco Community Edition"
published: false
categories: [alfresco]
---
> Before you start, validate that you have access to the prerequisite software so you can install them in the right order. This includes a JRE, a supported database, Tomcat application server, a message broker (i.e. ActiveMQ), Alfresco Search Services, and additional components (such as ImageMagick).

### Install JRE 11.0
```shell
$ sudo apt install default-jre
...
$ java --version
openjdk 11.0.4 2019-07-16
```

### Install MySQL 8.0

```shell
# Download repository
$ curl -OL https://dev.mysql.com/get/mysql-apt-config_0.8.14-1_all.deb

# Install MySQL repository package.
$ sudo dpkg -i mysql-apt-config_0.8.14-1_all.deb
# Scroll down and select the last option - **"Ok"**
$ sudo apt update
# Install MySQL server
$ sudo apt install mysql-server
```

### [Preparing MySQL database](https://docs.alfresco.com/community/tasks/mysql-config.html)

* Create a database with the **UTF-8** character set and the **utf8_bin** collation
```sql
mysql> CREATE DATABASE alfresco character set UTF8 collate utf8_bin;
```
* Create a new MySQL user
```sql
mysql> CREATE USER 'alfresco'@'localhost' IDENTIFIED BY 'alfresco';
```

* Grant the all privileges of the database
```sql
mysql> GRANT ALL PRIVILEGES ON alfresco.* TO 'alfresco'@'localhost';
mysql> FLUSH PRIVILEGES;
```

* Increase the maximum connections
```mysql
mysql> show variables like "max_connections";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 151   |
+-----------------+-------+

mysql> SET PERSIST max_connections = 275;
mysql> show variables like "max_connections";
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 275   |
+-----------------+-------+
```
The file **mysqld-auto.cnf** is created the first time a **SET PERSIST** statement is executed. Further SET PERSIST statement executions will append the contents to this file.  **RESET PERSIST** removes persisted settings from **mysqld-auto.cnf**

### [Install Tomcat 9](https://linuxize.com/post/how-to-install-tomcat-9-on-ubuntu-18-04/)
* Create Tomcat User
```shell
$ sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat
```

* Install Tomcat
```shell
# download the Tomcat archive
$ wget http://www-eu.apache.org/dist/tomcat/tomcat-9/v9.0.29/bin/apache-tomcat-9.0.29.tar.gz -P /tmp

# extract the Tomcat archive
$ sudo tar xf /tmp/apache-tomcat-9*.tar.gz -C /opt/tomcat

# create symbolic link
$ sudo ln -s /opt/tomcat/apache-tomcat-9.0.29 /opt/tomcat/latest

# changes the directory ownership
$ sudo chown -RH tomcat: /opt/tomcat/latest

# scripts inside bin directory must have executable flag:
$ sudo sh -c 'chmod +x /opt/tomcat/latest/bin/*.sh'
```

### Create a systemd Tomcat Unit File
* create a file named `tomcat.service`
```shell
$ sudo nano /etc/systemd/system/tomcat.service
```
Paste the following configuration:
```
[Unit]
Description=Tomcat 9 servlet container
After=network.target

[Service]
Type=forking

User=tomcat
Group=tomcat

Environment="JAVA_HOME=/usr/lib/jvm/default-java"
Environment="JAVA_OPTS=-Djava.security.egd=file:///dev/urandom -Djava.awt.headless=true"

Environment="CATALINA_BASE=/opt/tomcat/latest"
Environment="CATALINA_HOME=/opt/tomcat/latest"
Environment="CATALINA_PID=/opt/tomcat/latest/temp/tomcat.pid"
Environment="CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC"

ExecStart=/opt/tomcat/latest/bin/startup.sh
ExecStop=/opt/tomcat/latest/bin/shutdown.sh

[Install]
WantedBy=multi-user.target
```

### JVM settings
alfresco version 5.x `../tomcat/bin/setenv.sh`
```
...
JAVA_OPTS="-Xms4G -Xmx8G $JAVA_OPTS " # java-memory-settings
...
```
* `Xmx` specifies the **maximum memory** allocation pool for a Java virtual machine (JVM),
* `Xms` specifies the **initial memory** allocation pool.

* notify systemd that we created a new unit file
```shell
$ sudo systemctl daemon-reload
```

* Start the Tomcat service
```shell
$ sudo systemctl start tomcat
```

* Check the service status
```shell
$ sudo systemctl status tomcat
```

*  enable the Tomcat service to be automatically started at boot time
```shell
$ sudo systemctl enable tomcat
```

### Configure Tomcat Web Management Interface
* add a new user with access to the Tomcat web interface
```shell
$ sudo nano /opt/tomcat/latest/conf/tomcat-users.xml
```
*/opt/tomcat/latest/conf/tomcat-users.xml:*
```
<tomcat-users>
<!--
     add a new user with access to the Tomcat web interface
-->
   <role rolename="admin-gui"/>
   <role rolename="manager-gui"/>
   <user username="admin" password="admin_password" roles="admin-gui,manager-gui"/>
</tomcat-users>
```

* enable access to the web interface from anywhere
```shell
# Manager app
$ sudo nano /opt/tomcat/latest/webapps/manager/META-INF/context.xml
#Host Manager app
$ sudo nano /opt/tomcat/latest/webapps/host-manager/META-INF/context.xml
```
*context.xml:*
comment or remove the lines
```
<Context antiResourceLocking="false" privileged="true" >
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->
</Context>
```

### Copy the JDBC drivers for the database
* Download Connector/J 8.0.18 from the [MySQL download site](https://dev.mysql.com/downloads/connector/j/):
* Select Operating System: **Platform Independent**
    ```
    $ wget https://cdn.mysql.com//Downloads/Connector-J/mysql-connector-java-8.0.18.tar.gz

    # Extracting .tar.gz files
    $ tar xvzf mysql-connector-java-8.0.18.tar.gz
    ```
* Copy the **JAR** file into the /lib directory. For example, for Tomcat, copy the JAR file into the `<TOMCAT_HOME>/lib` directory.
```
# copy jar file to `<TOMCAT_HOME>/lib`
$ sudo cp mysql-connector-java-8.0.18/mysql-connector-java-8.0.18.jar /opt/tomcat/latest/lib/

# change owner
$ sudo chown tomcat:tomcat /opt/tomcat/latest/lib/mysql-connector-java-8.0.18.jar
```


### Create an additional classpath to Tomcat
* Create the directories required for an Alfresco Community Edition installation under `<TOMCAT_HOME>`:
```shell
$ sudo mkdir /opt/tomcat/latest/shared

# Create the shared/classes directory.
$ sudo mkdir /opt/tomcat/latest/shared/classes

# Create the shared/lib directory.
$ sudo mkdir /opt/tomcat/latest/shared/lib

# # change owner
$ sudo chown -R tomcat:tomcat /opt/tomcat/latest/shared/
```

* Open the `<TOMCAT_HOME>/conf/catalina.properties` file.
* Change the value of the` shared.loader=` property to the following:

*<TOMCAT_HOME>/conf/catalina.properties:*
```
shared.loader=${catalina.base}/shared/classes
```
### [Installing the Alfresco WARs](https://docs.alfresco.com/community/tasks/alf-war-install.html)
* Download the following file:
```
$ wget https://download.alfresco.com/cloudfront/release/community/201911-GA-build-368/alfresco-content-services-community-distribution-6.2.0-ga.zip
```
* extract
```
$ sudo unzip alfresco-content-services-community-distribution-6.2.0-ga.zip -d /opt/

# rename folder and change owner
$ cd /opt
$ sudo mv alfresco-content-services-community-distribution-6.2.0-ga/ alfresco/
$ sudo chown -R tomcat:tomcat alfresco/
```
* Move the WAR files from `/web-server/webapps` to the `<TOMCAT_HOME>/webapps` directory.
```shell
$ sudo cp /opt/alfresco/web-server/webapps/*.* /opt/tomcat/latest/webapps/
$ $ sudo ls -la /opt/tomcat/latest/webapps/
total 243188
drwxr-x--- 10 tomcat tomcat        14 Dec 13 04:41 .
drwxr-xr-x  9 tomcat tomcat        16 Dec 12 07:44 ..
drwxr-x---  6 tomcat tomcat         9 Dec 13 04:41 ROOT
-rw-rw-r--  1 tomcat tomcat    275031 Nov 27 09:44 ROOT.war
drwxr-x---  4 tomcat tomcat         5 Dec 13 04:41 _vti_bin
-rw-rw-r--  1 tomcat tomcat    762845 Nov 27 09:45 _vti_bin.war
drwxr-x--- 10 tomcat tomcat        13 Dec 13 04:41 alfresco
-rw-rw-r--  1 tomcat tomcat 174557259 Nov 27 09:44 alfresco.war
drwxr-x--- 15 tomcat tomcat        60 Dec 12 07:44 docs
drwxr-x---  6 tomcat tomcat         7 Dec 12 07:44 examples
drwxr-x---  5 tomcat tomcat         7 Dec 12 07:44 host-manager
drwxr-x---  5 tomcat tomcat         8 Dec 12 07:44 manager
drwxr-x--- 14 tomcat tomcat        18 Dec 13 04:41 share
-rw-rw-r--  1 tomcat tomcat  75575656 Oct 25 16:29 share.war
```

* Move the contents from **/conf**, and **/lib** under **/web-serve** to the existing directories under <TOMCAT_HOME>.
```shell
$ sudo cp /opt/alfresco/web-server/conf/Catalina/localhost/*.* /opt/tomcat/latest/conf/Catalina/localhost/
# change ownership
$ sudo chown -R tomcat:tomcat /opt/tomcat/latest
```

* Set the global properties. Copy `/web-server/shared/classes/alfresco-global.properties.sample` to `<TOMCAT_HOME>/shared/classes`
```shell
$ sudo cp /opt/alfresco/web-server/shared/classes/alfresco-global.properties.sample /opt/tomcat/latest/shared/classes/alfresco-global.properties
$ sudo chown  tomcat:tomcat /opt/tomcat/latest/shared/classes/alfresco-global.properties
$ sudo nano /opt/tomcat/latest/shared/classes/alfresco-global.properties
```

Replace the sample configuration with the following:
```
#
# Set this property unless you have explicitly chosen to expose some repository APIs without authentication
solr.secureComms=https

#
# Custom content and index data location
#
dir.root=/opt/alfresco/alf_data
dir.keystore=${dir.root}/keystore

#
# Sample database connection properties
#
db.username=alfresco
db.password=alfresco

#
# MySQL connection
#
db.driver=org.gjt.mm.mysql.Driver
db.url=jdbc:mysql://localhost/alfresco?useUnicode=yes&characterEncoding=UTF-8

#
# URL Generation Parameters (The ${localname} token is replaced by the local server name)
#-------------
alfresco.context=alfresco
alfresco.host=${localname}
alfresco.port=8080
alfresco.protocol=http
share.context=share
share.host=${localname}
share.port=8080
share.protocol=http
```
Save the file


###  Configure mutual TLS for Solr communication.

### Reference
* [Installing using distribution zip](https://docs.alfresco.com/community/concepts/install-community-intro.html)
* [Configuring databases](https://docs.alfresco.com/community/concepts/intro-db-setup.html)
* [Alfresco Ubuntu Installer](https://github.com/loftuxab/alfresco-ubuntu-install)
