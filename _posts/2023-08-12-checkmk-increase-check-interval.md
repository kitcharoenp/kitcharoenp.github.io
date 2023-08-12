---
layout : post
title : "Increase checkmk check interval"
categories : [monitor, checkmk]
published : true
---

Increase checkmk check interval to a longer interval in order to save CPU resources on the server and target systems 

### Search check interval
Search `check interval` from the **"Setup"** from checkmk interface

![checkmk-increase-check-interval-00](/assets/img/2023-08/checkmk-increase-check-interval-00.png)

### Host: Normal check interval for host checks
> The default interval is set to 6 seconds for smart ping and one minute for all other. Here you can specify a larger interval. The host is contacted in this interval on a regular base. The host check is also being executed when a problematic service state is detected to check wether or not the service problem is resulting from a host problem.

Add rule: **Normal check interval for host checks**. Increase Normal check interval for host checks to **10 minutes.** 

![checkmk-increase-check-interval-01](/assets/img/2023-08/checkmk-increase-check-interval-01.png)

Click **Save**

![checkmk-increase-check-interval-02](/assets/img/2023-08/checkmk-increase-check-interval-02.png)


### Service: Normal check interval for service checks
> Checkmk usually uses an interval of one minute for the active Checkmk check and for legacy checks. Here you can specify a larger interval.

Add rule: **Normal check interval for service checks**. Increase Normal check interval for service checks to **15 minutes.** 

![checkmk-increase-check-interval-03](/assets/img/2023-08/checkmk-increase-check-interval-03.png)

Click **Save**

![checkmk-increase-check-interval-04](/assets/img/2023-08/checkmk-increase-check-interval-04.png)

### Activate pending changes

1. click the **yellow stop sign** at the top right corner 
2. click the **Activate on selected sites**

![checkmk-increase-check-interval-05](/assets/img/2023-08/checkmk-increase-check-interval-05.png)


![checkmk-increase-check-interval-06](/assets/img/2023-08/checkmk-increase-check-interval-06.png)

### Reference
* [Increase checkmk check interval to a longer interval](https://blog.milliondollarserver.com/2021/12/increase-checkmk-check-interval-to.html)