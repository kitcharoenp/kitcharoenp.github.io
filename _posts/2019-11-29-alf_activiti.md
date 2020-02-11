---
layout: post
title: "Alfresco : Activiti User Guide"
published: false
categories: [alfresco]
---

### [Activiti User Guide](https://www.activiti.org/userguide/6.latest/index.html#demo.setup.one.minute.version)

### Required software
* [Apache Tomcat](https://linuxize.com/post/how-to-install-tomcat-9-on-ubuntu-18-04/)

### Error due to java 11
```
ERROR org.activiti.app.conf.SecurityConfiguration - Could not configure authentication mechanism:
```

### [Trouble deploying activiti-app (activiti 6) on Tomcat](https://hub.alfresco.com/t5/alfresco-process-services/trouble-deploying-activiti-app-activiti-6-on-tomcat/m-p/179606)

### [Downgrading Java 11 to java 8](https://askubuntu.com/questions/1133216/downgrading-java-11-to-java-8)
```
$ sudo update-alternatives --config java
$: command not found
kitcharoenp:~$ sudo update-alternatives --config java
There are 3 choices for the alternative java (providing /usr/bin/java).

  Selection    Path                                            Priority   Status
------------------------------------------------------------
* 0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1101      auto mode
  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1101      manual mode
  2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode
  3            /usr/lib/jvm/java-9-openjdk-amd64/bin/java       1091      manual mode

Press <enter> to keep the current choice[*], or type selection number: 2
update-alternatives: using /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java to provide /usr/bin/java (java) in manual mode
kitcharoenp:~$ java --version
Unrecognized option: --version
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
kitcharoenp:~$ java -v
Unrecognized option: -v
Error: Could not create the Java Virtual Machine.
Error: A fatal exception has occurred. Program will exit.
kitcharoenp:~$ java -version
openjdk version "1.8.0_222"
OpenJDK Runtime Environment (build 1.8.0_222-8u222-b10-1ubuntu1~18.04.1-b10)
OpenJDK 64-Bit Server VM (build 25.222-b10, mixed mode)
kitcharoenp:~$
```

### Activiti Kickstart App
* [Activiti Kickstart App and Activiti Rest Webapp](https://www.baeldung.com/activiti-kickstart-and-rest-apps)
* [Activiti User Guide](https://docs.alfresco.com/activiti/docs/user-guide/1.4.0/#app-creating-app)
* [BPMN] ลองใช้ Process ที่ได้เพิ่งสร้างกัน](https://naiwaen.debuggingsoft.com/2018/01/bpmn-try-sample-appove-budget-processs/)
* [Advanced Workflow สำหรับ Alfresco](https://thaiopensource.org/advanced-workflow-%E0%B8%AA%E0%B8%B3%E0%B8%AB%E0%B8%A3%E0%B8%B1%E0%B8%9A-alfresco/)
