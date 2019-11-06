---
layout: post
title: "Running supervisord automatically on startup"
published: true
categories: [celery]
---
### [Starting the supervisor server](https://www.vultr.com/docs/installing-and-configuring-supervisor-on-ubuntu-16-04)
> supervisor is composed of a server and clients that connect to it. To be able to manage and control programs, we need to start the server. To do so, we will be registering the supervisor server in systemd, so that the server may be started at system startup.

## Registering the supervisor server in `systemd`

* Create the `supervisord.service`  in the `/etc/systemd/system` directory.

```shell
$ sudo touch /etc/systemd/system/supervisord.service
```

* Add contents

```shell
[Unit]
Description=Supervisor daemon
Documentation=http://supervisord.org
After=network.target

[Service]
ExecStart=/usr/local/bin/supervisord -n -c /etc/supervisor/supervisord.conf
ExecStop=/usr/local/bin/supervisorctl $OPTIONS shutdown
ExecReload=/usr/local/bin/supervisorctl $OPTIONS reload
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
Alias=supervisord.service
```

* Activate service

```shell
$ sudo systemctl start supervisord.service
```

* Check service status

```shell
$ systemctl status supervisord.service
● supervisord.service - Supervisor daemon
   Loaded: loaded (/etc/systemd/system/supervisord.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2019-05-23 09:07:26 +07; 3h 3min ago
     Docs: http://supervisord.org
 Main PID: 2398 (supervisord)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/supervisord.service
           ├─2398 /usr/bin/python3 /usr/local/bin/supervisord -n -c /etc/supervisor/supervisord.conf
...           
```

### Resolve `supervisord` is not `active (running)`
```shell
$ sudo service supervisord status
● supervisord.service - Supervisor daemon
   Loaded: loaded (/etc/systemd/system/supervisord.service; enabled; vendor preset: enabled)
   Active: activating (auto-restart) (Result: exit-code) since Tue 2019-05-28 08:55:01 UTC; 3s ago
     Docs: http://supervisord.org
  Process: 22728 ExecStart=/usr/local/bin/supervisord -n -c /etc/supervisor/supervisord.conf (code
 Main PID: 22728 (code=exited, status=2)

# list runing process
$ sudo ps -aux | grep super
root     21280  0.0  0.2  79260 19880 ?        Ss   08:36   0:00 /usr/bin/python3 /usr/local/bin/supervisord
root     22645  0.0  0.0   7960   648 ?        S+   08:54   0:00 tail -f -n 200 /tmp/supervisord.log
root     22756  0.0  0.0  14856   808 ?        S+   08:55   0:00 grep --color=auto super

# kill supervisord process
$ sudo kill -9 21280
$ sudo service supervisord status
● supervisord.service - Supervisor daemon
   Loaded: loaded (/etc/systemd/system/supervisord.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2019-05-28 08:55:43 UTC; 1s ago
     Docs: http://supervisord.org
 Main PID: 22778 (supervisord)
    Tasks: 1 (limit: 4915)
   CGroup: /system.slice/supervisord.service
           └─22778 /usr/bin/python3 /usr/local/bin/supervisord -n -c /etc/supervisor/supervisord.conf
```

### Remove the default supervisor init-scripts
If install supervisor from distribute package
```shell
$ sudo update-rc.d -f supervisor remove
$ sudo rm -f /etc/init.d/supervisor
$ sudo rm -f /etc/default/supervisor
```

### Restart server
```shell
$ sudo shutdown -r now

```
