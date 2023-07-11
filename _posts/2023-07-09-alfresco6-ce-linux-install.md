---
layout : post
title : "Install Alfresco 6.x Community Edition"
categories : [alfresco]
published : true
---

### [Download Linux Installer](https://hub.alfresco.com/t5/alfresco-content-services-hub/alfresco-community-edition-201802-ea-file-list/ba-p/291521)
```shell
$ sudo wget https://download.alfresco.com/cloudfront/release/community/201802-EA-build-00115/alfresco-content-services-community-full-installer-6.0.2.1-ea-linux-x64.bin
```

###  [Install all of the libraries needed to support LibreOffice](https://www.vultr.com/docs/how-to-install-alfresco-community-edition-on-ubuntu-16-04/)

```shell
# fontconfig libSM libICE libXrender libXext libcups libGLU libcairo2 libgl1-mesa-glx 
$ sudo apt update
$ sudo apt -y install fontconfig libsm-dev libice-dev libxt-dev libxrender-dev libxext-dev cups libglu1-mesa-dev libcairo2-dev libgl-dev
```

When the libraries were not found on your system . You will get below messages

```shell 
$ sudo ./alfresco-content-services-community-full-installer-6.0.2.1-ea-linux-x64.bin 
Some or all of the libraries needed to support LibreOffice were not found on your system: fontconfig libSM libICE libXrender libXext libcups libGLU libcairo2 libgl1-mesa-glx
You are strongly advised to stop this installation and install the libraries.
```

### Run Linux Installer
```shell
$ sudo ./alfresco-content-services-community-full-installer-6.0.2.1-ea-linux-x64.bin 
```

### Language Selection
```
Language Selection

Please select the installation language
[1] English - English
[2] French - Français
[3] Spanish - Español
[4] Italian - Italiano
[5] German - Deutsch
[6] Japanese - 日本語
[7] Dutch - Nederlands
[8] Russian - Русский
[9] Simplified Chinese - 简体中文
[10] Norwegian - Norsk bokmål
[11] Brazilian Portuguese - Português Brasileiro
Please choose an option [1] : 1
```

Installation Type

```shell
----------------------------------------------------------------------------
Welcome to the Alfresco Content Services Community Setup Wizard.

----------------------------------------------------------------------------
Installation Type

[1] Easy - Install using the default configuration.
[2] Advanced - Configure server ports and service properties.: Choose optional components to install.
Please choose an option [1] : 2
```

Select the components you want to install

```
Select the components you want to install; clear the components you do not want 
to install. Click Next when you are ready to continue.

Java [Y/n] :

PostgreSQL [Y/n] :

LibreOffice [Y/n] :

Alfresco Content Services Community : Y (Cannot be edited)

Solr1 [y/N] : 

Solr4 [Y/n] :

Alfresco Office Services [Y/n] :

Web Quick Start [y/N] : 

Google Docs Integration [Y/n] :

Is the selection above correct? [Y/n]:
```


----------------------------------------------------------------------------
Installation Folder

Choose a folder to install Alfresco Content Services Community.

Select a folder: [/opt/alfresco-content-services-community-full]:
----------------------------------------------------------------------------
Database Server Parameters

Enter the port of your database.

Database Server Port: [5432]: 

----------------------------------------------------------------------------
Tomcat Port Configuration

Enter your Tomcat configuration parameters.

Web Server Domain: [127.0.0.1]: 

Tomcat Server Port: [8080]: 

Tomcat Shutdown Port: [8005]: 

Tomcat SSL Port: [8443]: 

Tomcat AJP Port: [8009]: 

----------------------------------------------------------------------------
LibreOffice Server Port

Enter the port that the LibreOffice Server will listen to.

LibreOffice Server Port: [8100]: 

----------------------------------------------------------------------------
FTP Port

Choose a port number for the integrated FTP server.

Port: [21]: 

----------------------------------------------------------------------------
Admin Password

Specify a password for the Alfresco Content Services administrator account.

Admin Password: :
Repeat Password: :
----------------------------------------------------------------------------
Install as a service

If you register Alfresco Content Services Community as a service it will 
automatically start Alfresco Content Services Community on machine startup.

Install Alfresco Content Services Community as a service? [Y/n]: 


----------------------------------------------------------------------------
Warning

This environment is not configured optimally for Alfresco Content Services - 
review this list before continuing.

While these issues won’t prevent Alfresco Content Services from functioning, 
some product features might not be available, or the system might not perform 
optimally.

CPU clock speed is too slow
 (2.0 GHz+): 1.6 GHz


 Press [Enter] to continue:

----------------------------------------------------------------------------
Setup is now ready to begin installing Alfresco Content Services Community on 
your computer.

Do you want to continue? [Y/n]: 


 ----------------------------------------------------------------------------
Please wait while Setup installs Alfresco Content Services Community on your 
computer.

 Installing
 0% ______________ 50% ______________ 100%
 #########################################

----------------------------------------------------------------------------
Setup has finished installing Alfresco Content Services Community on your 
computer.

View Readme File [Y/n]: Y

Launch Alfresco Content Services Community [Y/n]: 

waiting for server to start....README
Alfresco Community (Build: 6.0.2.1-ea)
===============================

Contains:
	- Alfresco Content Services Community:	6.0.2-ea
	- Alfresco Share:		5.2.f

For users of Alfresco Community Edition, more information on this release is 
available at https://community.alfresco.com/community/ecm

Press [Enter] to continue: done
server started
/opt/alfresco-content-services-community-full/postgresql/scripts/ctl.sh : postgresql  started at port 5432
Using CATALINA_BASE:   /opt/alfresco-content-services-community-full/tomcat
Using CATALINA_HOME:   /opt/alfresco-content-services-community-full/tomcat
Using CATALINA_TMPDIR: /opt/alfresco-content-services-community-full/tomcat/temp
Using JRE_HOME:        /opt/alfresco-content-services-community-full/java
Using CLASSPATH:       /opt/alfresco-content-services-community-full/tomcat/bin/bootstrap.jar:/opt/alfresco-content-services-community-full/tomcat/bin/tomcat-juli.jar
Using CATALINA_PID:    /opt/alfresco-content-services-community-full/tomcat/temp/catalina.pid
Tomcat started.
/opt/alfresco-content-services-community-full/tomcat/scripts/ctl.sh : tomcat started




