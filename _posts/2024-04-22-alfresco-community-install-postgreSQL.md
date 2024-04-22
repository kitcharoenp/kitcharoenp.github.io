---
layout : post
title : "Alfresco Community 23.x Install PostgreSQL 15.x"
categories : [alfreco]
published : true
---
### Alfresco Supported platforms
[Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Install Initial Packages

```shell
sudo apt install dirmngr ca-certificates software-properties-common apt-transport-https lsb-release curl -y
```

### Import Repository

```shell
# import the PostgreSQL GPG key
curl -fSsL https://www.postgresql.org/media/keys/ACCC4CF8.asc | gpg --dearmor | sudo tee /usr/share/keyrings/postgresql.gpg > /dev/null

# Import stable APT repository

echo deb [arch=amd64,arm64,ppc64el signed-by=/usr/share/keyrings/postgresql.gpg] http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main | sudo tee /etc/apt/sources.list.d/postgresql.list

```

### Install PostgreSQL 15

```shell
# Update your repository sources list 
sudo apt update

# install PostgreSQL
sudo apt install postgresql-client-15 postgresql-15

# verify the software’s status 
systemctl status postgresql
```
**Output:**

```shell
● postgresql.service - PostgreSQL RDBMS
     Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor preset: enabled)
     Active: active (exited) since Mon 2024-04-22 07:13:14 UTC; 8s ago
    Process: 391 ExecStart=/bin/true (code=exited, status=0/SUCCESS)
   Main PID: 391 (code=exited, status=0/SUCCESS)
        CPU: 2ms
```

### Reference
* [How to Install PostgreSQL 15 on Ubuntu 24.04, 22.04 or 20.04](https://www.linuxcapable.com/install-postgresql-15-on-ubuntu/)