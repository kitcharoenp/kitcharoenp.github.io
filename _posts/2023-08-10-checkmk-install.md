---
layout : post
title : "Checkmk on Ubuntu 22.04"
categories : [ubuntu, monitor, checkmk]
published : false
---


### 

```
No user sessions are running outdated binaries.

No VM guests are running outdated hypervisor (qemu) binaries on this host.
N: Download is performed unsandboxed as root as file '/home/ubuntu/check-mk-free-2.1.0p2_0.jammy_amd64.deb' couldn't be accessed by user '_apt'. - pkgAcquire::Run (13: Permission denied)
```

### Create a monitoring instance

```shell
ubuntu@checkmk-server0x:~$ sudo omd create Server0xMonitor
Creating temporary filesystem /omd/sites/Server0xMonitor/tmp...mount: /opt/omd/sites/Server0xMonitor/tmp: can't find in /etc/fstab.
WARNING: You may continue without tmpfs, but the performance of Check_MK may be degraded.
Updating core configuration...
Generating configuration for core (type cmc)...
Starting full compilation for all hosts Creating global helper config...OK
 Creating cmc protobuf configuration...OK
Executing post-create script "01_create-sample-config.py"...OK
Restarting Apache...OK
Created new site Server0xMonitor with version 2.1.0p2.cfe.

  The site can be started with omd start Server0xMonitor.
  The default web UI is available at http://checkmk-server0x/Server0xMonitor/

  The admin user for the web applications is cmkadmin with password: kIVsAcy0
  For command line administration of the site, log in with 'omd su Server0xMonitor'.
  After logging in, you can change the password for cmkadmin with 'htpasswd etc/htpasswd cmkadmin'.
```

### Install Agent

```shell
ubuntu@server01:~$ sudo dpkg -i check-mk-agent_2.1.0p2-567398bf0c1b95ff_all.deb 
Selecting previously unselected package check-mk-agent.
(Reading database ... 74525 files and directories currently installed.)
Preparing to unpack check-mk-agent_2.1.0p2-567398bf0c1b95ff_all.deb ...
Unpacking check-mk-agent (2.1.0p2-1.567398bf0c1b95ff) ...
Setting up check-mk-agent (2.1.0p2-1.567398bf0c1b95ff) ...

Deploying systemd units: check-mk-agent-async.service check-mk-agent.socket check-mk-agent@.service cmk-agent-ctl-daemon.service
Deployed systemd
Creating/updating cmk-agent user account ...

WARNING: The agent controller is operating in an insecure mode! To secure the connection run `cmk-agent-ctl register`.

Activating systemd unit 'check-mk-agent-async.service'...
Created symlink /etc/systemd/system/multi-user.target.wants/check-mk-agent-async.service → /lib/systemd/system/check-mk-agent-async.service.
Activating systemd unit 'check-mk-agent.socket'...
Created symlink /etc/systemd/system/sockets.target.wants/check-mk-agent.socket → /lib/systemd/system/check-mk-agent.socket.
Activating systemd unit 'cmk-agent-ctl-daemon.service'...
Created symlink /etc/systemd/system/multi-user.target.wants/cmk-agent-ctl-daemon.service → /lib/systemd/system/cmk-agent-ctl-daemon.service.
ubuntu@server01:~$
```

### [Notifications](https://docs.checkmk.com/latest/en/notifications.html)



```
Unhandled exception: Failed to send the mail: /usr/sbin/sendmail is missing
```

### Reference
* [How to install the latest version of Checkmk on Ubuntu 22.04](https://www.techrepublic.com/article/install-latest-checkmk-ubuntu/)

* [How to Install and Monitor Servers with Checkmk on Ubuntu 22.04](https://www.howtoforge.com/how-to-install-and-monitor-servers-with-checkmk-on-ubuntu-22-04/)

* [How To Install and Configure Postfix as a Send-Only SMTP Server on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-postfix-as-a-send-only-smtp-server-on-ubuntu-22-04)

* [How to Configure Postfix to Use External SMTP](https://phoenixnap.com/kb/postfix-smtp)