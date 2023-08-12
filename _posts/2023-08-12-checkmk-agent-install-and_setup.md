---
layout : post
title : "Checkmk Agent Install and Setup"
categories : [monitor, checkmk]
published : false
---
### Install the agent

On the Checkmk main site, go to `Setup | Agents | Linux`. Downloaded the **Packaged Agents:** `check-mk-agent_2.2.0p7-1_all.deb` installer to the hosting server


![check_mk_agent_linux](/assets/img/blog/check_mk_agent_linux.png)


```bash
$ sudo apt install ./check-mk-agent_2.2.0p7-1_all.deb
```

```bash
$ sudo apt install ./check-mk-agent_2.2.0p7-1_all.deb 
[sudo] password for ubuntu: 
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
Note, selecting 'check-mk-agent' instead of './check-mk-agent_2.2.0p7-1_all.deb'
...
Preparing to unpack .../check-mk-agent_2.2.0p7-1_all.deb ...
Unpacking check-mk-agent (2.2.0p7-1) ...
Setting up check-mk-agent (2.2.0p7-1) ...

Deploying agent controller: /usr/bin/cmk-agent-ctl
Deploying systemd units: check-mk-agent@.service check-mk-agent-async.service cmk-agent-ctl-daemon.service check-mk-agent.socket
...        
```

### Add Hosts to Checkmk
On the Checkmk main site, go to `Setup | Hosts | Add host to the monitoring`.

![checkmk_add_host_monitoring](/assets/img/blog/checkmk_add_host_monitoring.png)


![checkmk_add_host_monitoring_01](/assets/img/blog/checkmk_add_host_monitoring_01.png)


* Type the **Hostname** for the new host: `server01` 
* Click the checkbox for **IPv4 Address** and type the IP address for the host : `10.10.10.11`
* Click **Save & run service discovery**

### Accept Services Monitoring of host 

+/- Services for Monitoring

![checkmk_add_host_monitoring_02](/assets/img/blog/checkmk_add_host_monitoring_02.png)

click **Accept All** 

### Activate pending changes

1. click the **yellow stop sign** at the top right corner 
2. click the **red circle**

![check_mk_Activate_pending_changes](/assets/img/blog/check_mk_Activate_pending_changes.png)

![check_mk_Activate_pending_changes_01](/assets/img/blog/check_mk_Activate_pending_changes_01.png)


### Reference
* [How to install the latest version of Checkmk on Ubuntu 22.04](https://www.techrepublic.com/article/install-latest-checkmk-ubuntu/)

* [Downloading & Installing Checkmk](https://checkmk.com/download?utm_content=headerCta)

* [How to Install and Monitor Servers with Checkmk on Ubuntu 22.04](https://www.howtoforge.com/how-to-install-and-monitor-servers-with-checkmk-on-ubuntu-22-04/)