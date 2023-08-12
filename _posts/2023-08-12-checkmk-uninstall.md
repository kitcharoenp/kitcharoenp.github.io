---
layout : post
title : "Uninstall CheckMK on Ubuntu 22.04"
categories : [monitor, checkmk]
published : true
---

My monitor site name is `Server0xMonitor`

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

### Remove  the `Server0xMonitor` site:

```bash
$ sudo omd rm Server0xMonitor
```

```console
$ sudo omd rm Server0xMonitor
PLEASE NOTE: This action removes all configuration files
             and variable data of the site.

In detail the following steps will be done:
- Stop all processes of the site
- Unmount tmpfs of the site
- Remove tmpfs of the site from fstab
- Remove the system user <SITENAME>
- Remove the system group <SITENAME>
- Remove the site home directory
- Restart the system wide apache daemon
 [yes/NO]: yes
Removing Crontab...no crontab for Server0xMonitor
Stopping redis...Not running.
Stopping dcd...Not running.
Stopping apache...(not running)...OK
Stopping cmc...Not running.
Stopping rrdcached...not running.
Stopping mknotifyd...Not running.
Stopping liveproxyd...Not running.
Stopping mkeventd...Not running.
Stopping agent-receiver...not running.
Deleting user and group Server0xMonitor...OK
Restarting Apache...O
```

### Find the installed CheckMk
```bash
$ dpkg -l | grep check-mk
ii  check-mk-free-2.1.0p2           0.jammy                                         amd64        Checkmk - Best-in-class infrastructure & application monitoring
```


### [Uninstall CheckMK](https://linux.how2shout.com/how-to-install-checkmk-on-ubuntu-22-04-lts-jammy-linux/#10_Uninstall_CheckMK)

```bash
$ sudo dpkg --purge check-mk-free-2.1.0p2
```

```console
$ sudo dpkg --purge check-mk-free-2.1.0p2
(Reading database ... 74375 files and directories currently installed.)
Removing check-mk-free-2.1.0p2 (0.jammy) ...
update-alternatives: removing manually selected alternative - switching omd to auto mode
Purging configuration files for check-mk-free-2.1.0p2 (0.jammy) ...
Removing system group 'omd'
Removing global links/directories
Conf zzz_omd disabled.
To activate the new config

```


### Remove check-mk-agent
```bash
 $ sudo apt purge check-mk-agent 
```

### Reference

* [Uninstall CheckMK](https://linux.how2shout.com/how-to-install-checkmk-on-ubuntu-22-04-lts-jammy-linux/#10_Uninstall_CheckMK)