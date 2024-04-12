---
layout : post
title : "Alfresco Community 23.x Install Zip"
categories : [alfreco]
published : false
---
### Supported platforms
[Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Prerequisites

* Java: OpenJDK 17.x.x is required
* Application server: Apache Tomcat (Tomcat 10.1.x)
* Database: PostgreSQL 15.x
* Message broker: ActiveMQ v5.18
* LibreOffice v7.2.5
* ImageMagick v7.1.0-16

### Download ACS and ASS Distribution Packages
* [alfresco-content-services-community-distribution-23.2.1.zip](https://artifacts.alfresco.com/nexus/content/repositories/releases/org/alfresco/alfresco-content-services-community-distribution/23.2.1/)

* [alfresco-search-services-2.0.9.zip](https://artifacts.alfresco.com/nexus/content/repositories/releases/org/alfresco/alfresco-search-services/2.0.9/)


### [Create `users` and `groups`](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-usr-grp)

1. User and Group for **Alfresco Content Services** (ACS):

```shell
# Create a group named 'Alfresco'
$ sudo groupadd Alfresco

# Create a system user named 'alfresco'
$ sudo useradd --system alfresco

# Add the user 'alfresco' to the group 'Alfresco'
$ sudo usermod -a -G Alfresco alfresco

# Verify group membership
# Output :  alfresco : alfresco Alfresco
$ sudo groups alfresco 

```

2. User and Group for **Alfresco Search Services** (ASS)

```shell
# Create a group named 'Solr'
$ sudo groupadd Solr

# Create a system user named 'solr'
$ sudo useradd --system solr

# Add the user 'solr' to the group 'Solr'
$ sudo usermod -a -G Solr solr

# Verify group membership
# Output :  solr : solr Solr
$ sudo groups solr

```

### [Create Directory Structure](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-directory) Alfresco Community 23.x

#### Create directory structure for `Alfresco Content Services`

* Create a directory named `alfresco-community23x` under `/usr/local/` directory.

   ```shell
   $ sudo mkdir /usr/local/alfresco-community23x
   ```
* Create sub-directories under `/usr/local/alfresco-community23x`
   
   ```shell
   $ cd /usr/local/alfresco-community23x
   $ sudo mkdir -p activemq alf_data/keystore amps amps_share bin lincenses modules/platform modules/share
   
   # Local transformation services
   $ sudo mkdir -p alfresco-pdf-renderer imagemagick libreoffice tomcat/shared/classes tomcat/shared/lib
   ```

* Set initial permissions to structure

   ```shell
   $ sudo chgrp -R Alfresco /usr/local/alfresco-community23x

   $ sudo chmod 775 /usr/local/alfresco-community23x

   $ sudo chmod -R 775 /usr/local/alfresco-community23x/alf_data
   ```

* Add `ALF_HOME` to permanent environment variable, Edit (ubuntu)`~/.profile` file: 

   ```shell
   # Ubuntu
   $ sudo vim ~/.profile

   # Red Hat
   $ sudo vim ~/.bash_profile
   ```
   **~/.profile:**
   ```shell
   # .profile

   # Get the aliases and functions
   if [ -f ~/.bashrc ]; then
      . ~/.bashrc
   fi

   # User specific environment and startup programs

   export ALF_HOME=/usr/local/alfresco-community23x

   PATH=$PATH:$HOME/.local/bin:$HOME/bin:$ALF_HOME

   export PATH
   ```

#### Create directory structure for `Alfresco Search Services`

* Create a directory named `alfresco-search-services` under `/usr/local/` directory. 

```shell
$ sudo mkdir /usr/local/alfresco-search-services
```

* Set initial permissions to structure created folder 

```shell
$ sudo chgrp -R Solr /usr/local/alfresco-search-services

$ sudo chmod 775 /usr/local/alfresco-search-services 
```

* Add `SOLR_HOME` to permanent environment variable

 ```shell
   # Ubuntu
   $ sudo vim ~/.profile

   # Red Hat
   $ sudo vim ~/.bash_profile
```

Example `~/.profile` after add `ALF_HOME` and `SOLR_HOME` to permanent environment variable

```shell
# .profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi

# User specific environment and startup programs

export ALF_HOME=/usr/local/alfresco-community23x
export SOLR_HOME=/usr/local/alfresco-search-services

PATH=$PATH:$HOME/.local/bin:$HOME/bin:$ALF_HOME:$SOLR_HOME

export PATH
```

### Installing Prerequisites
1. Installing Java 11.0.11 (OpenJDK 17.x.x)

2. Installing PostgreSQL 13

3. Install ActiveMQ 5.16 (Mandatory to use Transformation service)

4. Install Tomcat

### Setting up ACS (alfresco content services) 

7. Configure ACS repository

8. Configure tomcat for ACS repository


### Setup Local Transformation Service 

1. Install Imagemagick 7.0.10

2. Install LibreOffice 6.3.5.2

3. Alfresco PDF Renderer Setup

4. Exiftool Setup

5. Update alfresco-global.properties to enable transformation service

6. Set transformation core aio launcher and steps to launch

7. Update `alfresco-global.properties` to add other misc. properties

8. [Share Config Custom Changes](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#share-config-custom-changes)


### [Setup and Configure ASS (Alfresco Search Services)](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-and-configure-ass)

* Unzip the “alfresco-search-services-2.0.1.zip” package.

* Update the permissions

* add the following configuration properties `alfresco-global.properties`

* Starting and stopping alfresco search service

   #### Optionals
   * enabling the multi language search support
   * enabling search suggestions 
   * setup search services on a different or remote machine
   * edit `solr.in.sh` 

   #### Full setup and config file:
   * `alfresco-global.properties`
   * `custom-log4j.properties`
   * `shared.properties`
   * `solr.in.sh` 

### Start and Test
1. Start AMQ
2. Start LocalTransformationService
3. Start DB
4. Start Alfresco
5. Start ASS
6. Created a script `start.sh`, use this to start all services instead of doing one by one.

### Reference
* [Alfresco Install with zip](https://docs.alfresco.com/content-services/community/install/zip/)

* [Setup ACS-7.x, ASS-2.x and Local Transformation Service using distribution package](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html)

* [Index of alfresco-content-services-community-distribution](https://artifacts.alfresco.com/nexus/content/repositories/releases/org/alfresco/alfresco-content-services-community-distribution/)

* [Index of `alfresco-search-services` for download](https://artifacts.alfresco.com/nexus/content/repositories/releases/org/alfresco/alfresco-search-services/)