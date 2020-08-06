---
layout: post
title: "SAP : HTTP WebService Timeout"
categories: [sap]
---

### Set HTTP web services with time outs parameters

**ICM Parameter:** [[1]]
It may happen that get a ‘Connection Timed out’ page as an answer a long time before your configured session timeout limit is reached. Usually this timeout is raised because of a processing timeout, which happens if you have long running applications.

```
icm/server_port_0 = PROT=HTTP,PORT=10$$,TIMEOUT=3600,PROCTIMEOUT=600
```
#### RZ10
![ProxySQL respo ](/assets/img/blog/2020-08-06/2020-08-06_a.png)

#### Parameters
![ProxySQL respo ](/assets/img/blog/2020-08-06/2020-08-06_b.png)

#### ICM Parameter
![ProxySQL respo ](/assets/img/blog/2020-08-06/2020-08-06_c.png)

#### Edit
![ProxySQL respo ](/assets/img/blog/2020-08-06/2020-08-06_d.png)

### [Activating, Saving and Checking Profiles][2]
Once you have finished maintaining a profile, you can check it for errors or inconsistencies, save it in the database, and then activate it. [[2]]

### Procedures
#### Activate profiles
1. On the initial screen in transaction **RZ10**, enter the name of the profile that you want to activate. Use input help possible to display the available profiles

2. Choose Profile → Activate.

3. Stop the SAP instance(s) in which you want the profile changes to take effect, and restart them.


#### Save profiles
1. Call the CCMS profile maintenance tool by choosing Administration → CCMS → Configuration → Profile Maintenance.

2.   Enter a profile name in the Profile field and choose Profile → Save.  

#### Check all profiles of the active server
On the initial screen in transaction RZ10, choose Utilities → Check all profiles → Of active servers.


[1]: https://blogs.sap.com/2012/09/06/consuming-web-service-in-sap-with-time-outs-parameters/ "HTTP web services with time outs"

[2]: https://help.sap.com/saphelp_sm71_sp13/helpdata/en/c4/3a6254505211d189550000e829fbbd/content.htm?no_cache=true "Activating, Saving and Checking Profiles "
