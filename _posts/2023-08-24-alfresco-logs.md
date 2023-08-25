---
layout : post
title : "Alfresco logs"
categories : [alfresco]
published : false
---

###  Tomcat rotate `catalina.out`

The Tomcat Server log file catalina.out grows and grows by default.

```bash
$ sudo vim /etc/logrotate.d/alfresco

/opt/alfresco/tomcat/logs/catalina.out {
  copytruncate
  daily
  rotate 10
  missingok
  dateext
  size 50M
}
```

> If logrotate is not used by the operating system you can define a cronjob with crontab -e for your Alfresco user (owner of java process) with:

```console
#IMPORTANT: As Alfresco user
$ crontab -e 

0 5 * * 1 /usr/sbin/logrotate /etc/logrotate.d/alfresco
```


### Clean logs periodically

```console
#!/bin/bash
# Crontab for your Alfresco user (owner of java process)
# 0 5 * * 1 /opt/alfresco/bin/clean-logs.sh

ALF_HOME=/opt/alfresco

LOGS_TOM=$ALF_HOME/tomcat/logs
LOGS_DAYS=30

find ${LOGS_TOM}/* -mtime +${LOGS_DAYS} -name \*.log\* -delete 2>/dev/null
```

### Disable Tomcat localhost logs (localhost_access_log*)

```console
$ vim $ALF_HOME/tomcat/conf/server.xml

<!--
        <Valve className="org.apache.catalina.valves.AccessLogValve" directory="logs"
               prefix="localhost_access_log" suffix=".txt"
               pattern="%h %l %u %t &quot;%r&quot; %s %b" />
-->
```


### Disable manager and host-manager logs

Edit `$ALF_HOME/tomcat/conf/logging.properties` using only:


```console
handlers = 1catalina.java.util.logging.FileHandler
```

and comment all related lines regarding:

* 2localhost.java.util.logging.FileHandler
* 3manager.java.util.logging.FileHandler
* 4host-manager.java.util.logging.FileHandler



### Reference
* [Simple tips about Alfresco logs](https://www.zylk.net/en/web-2-0/blog/-/blogs/simple-tips-about-alfresco-logs)
