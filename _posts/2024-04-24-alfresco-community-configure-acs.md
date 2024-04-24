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
```

**Update the file permissions:**:

```shell
# Change
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

### AMPs

### Scripts and Libraries

### Licenses

### Reference
* [Configure ACS repository](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-and-configure-acs)