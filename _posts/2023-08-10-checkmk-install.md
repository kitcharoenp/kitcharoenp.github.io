---
layout : post
title : "Checkmk Install on Ubuntu 22.04"
categories : [monitor, checkmk]
published : true
---


### [Downloading Checkmk for Ubuntu](https://checkmk.com/download?method=cmk&edition=cre&version=2.2.0p7&platform=ubuntu&os=jammy&type=cmk&google_analytics_user_id=)

Pull in the package using wget
```bash
$ wget https://download.checkmk.com/checkmk/2.2.0p7/check-mk-raw-2.2.0p7_0.jammy_amd64.deb
```

### Install

```bash
sudo apt install ./check-mk-raw-2.2.0p7_0.jammy_amd64.deb
```

### Show Version
```
$ omd versions
```
```
2.2.0p7.cre (default)
```


### Create a `Server0xMonitor` monitoring instance

```bash
$ sudo omd create Server0xMonitor
```


```console
Creating temporary filesystem /omd/sites/Server0xMonitor/tmp...mount: /opt/omd/sites/Server0xMonitor/tmp: can't find in /etc/fstab.
WARNING: You may continue without tmpfs, but the performance of Check_MK may be degraded.
Updating core configuration...
Generating configuration for core (type nagios)...
Precompiling host checks...OK
Executing post-create script "01_create-sample-config.py"...OK
Restarting Apache...OK
Created new site Server0xMonitor with version 2.2.0p7.cre.

  The site can be started with omd start Server0xMonitor.
  The default web UI is available at http://checkmk-server0x/Server0xMonitor/

  The admin user for the web applications is cmkadmin with password: FvMkTriL
  For command line administration of the site, log in with 'omd su Server0xMonitor'.
  After logging in, you can change the password for cmkadmin with 'cmk-passwd cmkadmin'.
```

### Start site
The site can be started with `omd start Server0xMonitor`.

```bash
$ sudo omd start Server0xMonitor
```


```bash
$ sudo omd start Server0xMonitor
Creating temporary filesystem /omd/sites/Server0xMonitor/tmp...mount: /opt/omd/sites/Server0xMonitor/tmp: can't find in /etc/fstab.
WARNING: You may continue without tmpfs, but the performance of Check_MK may be degraded.
Starting agent-receiver...OK
Starting mkeventd...OK
Starting rrdcached...OK
Starting npcd...OK
Starting nagios...OK
Starting apache...OK
Starting redis...OK
Initializing Crontab...O

```

### Reference
* [How to install the latest version of Checkmk on Ubuntu 22.04](https://www.techrepublic.com/article/install-latest-checkmk-ubuntu/)

* [How to Install and Monitor Servers with Checkmk on Ubuntu 22.04](https://www.howtoforge.com/how-to-install-and-monitor-servers-with-checkmk-on-ubuntu-22-04/)

* [How To Install and Configure Postfix as a Send-Only SMTP Server on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-22-04)

* [How to Configure Postfix to Use External SMTP](https://phoenixnap.com/kb/postfix-smtp)