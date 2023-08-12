---
layout : post
title : "Checkmk Upgrade to 2.2 on Ubuntu 22.04"
categories : [ubuntu, monitor, checkmk]
published : true
---

### [Downloading Checkmk for Ubuntu](https://checkmk.com/download?method=cmk&edition=cre&version=2.2.0p7&platform=ubuntu&os=jammy&type=cmk&google_analytics_user_id=)

Pull in the package using wget
```bash
$ wget https://download.checkmk.com/checkmk/2.2.0p7/check-mk-raw-2.2.0p7_0.jammy_amd64.deb
```

### Install the Checkmk package

Install the new version:
```bash
sudo apt install ./check-mk-raw-2.2.0p7_0.jammy_amd64.deb
```

### Show version
```bash
$ omd version
```

```console
OMD - Open Monitoring Distribution Version 2.2.0p7.cre
```


### Show status
```
$ sudo omd status
```

```console
$ sudo omd status
Doing 'status' on site Server0xMonitor:
agent-receiver: running
mkeventd:       running
liveproxyd:     running
mknotifyd:      running
rrdcached:      running
cmc:            running
apache:         running
dcd:            running
redis:          running
crontab:        running
-----------------------
```

### Stop the `Server0xMonitor` site:

```bash
$ sudo omd stop Server0xMonitor
```

```console
Removing Crontab...OK
Stopping redis...killing 502...OK
Stopping dcd...killing 492....OK
Stopping apache...killing 476.................OK
Stopping cmc...killing 389..........OK
Stopping rrdcached...waiting for termination...OK
Stopping mknotifyd...killing 363...OK
Stopping liveproxyd...killing 344....OK
Stopping mkeventd...killing 335...OK
Stopping agent-receiver...killing 323...OK
Stopping 1 remaining site processes...OK
```

### Update the `Server0xMonitor` site:

```bash
$ sudo omd update Server0xMonitor
```

### Reference

* [Update to version 2.2.0](https://docs.checkmk.com/latest/en/update_major.html)

* [ How to Upgrade check-mk to latest version on Debian 8 jessie ](https://blog.milliondollarserver.com/2018/09/how-to-upgrade-check-mk-to-latest.html)
