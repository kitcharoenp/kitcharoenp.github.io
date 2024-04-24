---
layout : post
title : "Alfresco Community 23.x Configure ACS"
categories : [alfreco]
published : true
---
## Configuration Alfresco Content Service
> **Alfresco Content Services** is a java/spring based product which is designed to manage content of enterprise (mainly documents). It manages documents with all its metadata and controls its access to users

### Unzip the package
Unzip files on home directory to `alfresco-content-services-23.2.1` directory

```shell
$ cd
$ unzip alfresco-content-services-community-distribution-23.2.1.zip -d acs-community-23.2.1
```

### Keystore
Copy contents from `keystore` to `/usr/local/alfresco-community23x/alf_data/keystore/`

* **CreateSSLKeystores.txt:**  Contains instructions to create an RSA public/private key pair for the repository with a certificate that has been signed by the Alfresco Certificate Authority (CA).

* **generate_keystores.sh:**  Shell script file for generating secure keys for SOLR communication.

* **readme.txt:**  Text file containing information about other files in a directory.


```shell
# CreateSSLKeystores.txt
$ sudo cp /home/ubuntu/acs-community-23.2.1/keystore/CreateSSLKeystores.txt /usr/local/alfresco-community23x/alf_data/keystore/

# generate_keystores.sh
$ sudo cp /home/ubuntu/acs-community-23.2.1/keystore/generate_keystores.sh /usr/local/alfresco-community23x/alf_data/keystore/

# readme.txt
$ sudo cp /home/ubuntu/acs-community-23.2.1/keystore/readme.txt /usr/local/alfresco-community23x/alf_data/keystore/

# Update the file permissions

# Change group
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/alf_data/keystore

# Change mode
$ sudo chmod -R 755 /usr/local/alfresco-community23x/alf_data/keystore

# Check permissions
$ ls -lt /usr/local/alfresco-community23x/alf_data/keystore/
total 15
-rwxr-xr-x 1 root Alfresco  580 Apr 24 03:35 readme.txt
-rwxr-xr-x 1 root Alfresco 5345 Apr 24 03:35 generate_keystores.sh
-rwxr-xr-x 1 root Alfresco 6417 Apr 24 03:35 CreateSSLKeystores.txt
```

### [Copy `alfresco-share-services.amp`](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#copy-amps)

> **alfresco-share-services.amp:** Alfresco share service module containing extensions for search, datalist, sample sites patch etc.

```shell
# Copy file
$ sudo cp /home/ubuntu/acs-community-23.2.1/amps/alfresco-share-services.amp /usr/local/alfresco-community23x/amps/

# Update the file permissions

$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/amps 
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/amps_share
$ sudo chmod -R 755 /usr/local/alfresco-community23x/amps
$ sudo chmod -R 755 /usr/local/alfresco-community23x/amps_share
```

### Scripts and Libraries

Copy the contents from `acs-community-23.2.1/bin` into `/usr/local/alfresco-community23x/bin` directory

* `alfresco-mmt.jar:` Alfresco Module Management Tool (MMT), A java library which supports alfresco module package installation/uninstallation/listing and preview operations etc.

* `apply_amps.sh:` Utility to install amps kept in `amps` and `amps_share` directory. It will install amps related to alfresco on alfresco.war (keeps the backup of original war file) and amps related to share on share.war (keeps the backup of original war file)

* `clean_tomcat.sh:` Shell script for cleaning out temporary application server files from previous installations.

```shell
# Copy alfresco-mmt.jar
$ sudo cp /home/ubuntu/acs-community-23.2.1/bin/alfresco-mmt.jar /usr/local/alfresco-community23x/bin/

# Copy apply_amps.sh
$ sudo cp /home/ubuntu/acs-community-23.2.1/bin/apply_amps.sh /usr/local/alfresco-community23x/bin/

# Copy clean_tomcat.sh
$ sudo cp /home/ubuntu/acs-community-23.2.1/bin/clean_tomcat.sh /usr/local/alfresco-community23x/bin/

# Update the directory permissions

# Change group
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/bin

# Change mode
$ sudo chmod -R 755 /usr/local/alfresco-community23x/bin

# Check permissions
$ ls -lt /usr/local/alfresco-community23x/bin/
total 2083
-rwxr-xr-x 1 root Alfresco     371 Apr 24 04:26 clean_tomcat.sh
-rwxr-xr-x 1 root Alfresco    2183 Apr 24 04:26 apply_amps.sh
-rwxr-xr-x 1 root Alfresco 2238309 Apr 24 04:26 alfresco-mmt.jar
```

### Licenses

It contains files that have information about license terms used by alfresco including all third party licenses.

```shell
# Copy licenses directory
$ sudo cp -R /home/ubuntu/acs-community-23.2.1/licenses/* /usr/local/alfresco-community23x/licenses/

# Update the directory permissions 
$ sudo chgrp -R Alfresco /usr/local/alfresco-community23x/licenses

# Check permissions
$ ls -lt /usr/local/alfresco-community23x/licenses/
total 27
drwxr-xr-x 2 root Alfresco    83 Apr 24 04:34 3rd-party
-rw-r--r-- 1 root Alfresco  7637 Apr 24 04:34 license.txt
-rw-r--r-- 1 root Alfresco 11152 Apr 24 04:34 notice.txt
```

### Reference
* [Configure ACS repository](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-and-configure-acs)