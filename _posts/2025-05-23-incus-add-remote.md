---
layout : post
title : "Adding a LXC remote server to Incus Server"
categories : [incus, lxc]
published : true
---

### Ccpy `client.crt` to LXC 
Transfer the `client.crt` from your user on the Incus server (look for it somewhere under `~/.config/incus/`), then transfer it to the lxc server

```shell
# List File
ls ~/.config/incus/
client.crt  client.key  config.yml  oidctokens  servercerts

# Copy `client.crt` to LXC Server
rsync -avp ~/.config/incus/client.crt ubuntu@lxc_server:/home/ubuntu
```

### Add Incus `client.crt` to the LXC trust store 

```shell
# add trust
lxc config trust add client.crt

# Displays the trust configurations for an LXD server.
lxc config trust list

+--------+------------------------+------------------------+--------------+
|  TYPE  |          NAME          |      COMMON NAME       | FINGERPRINT  |
+--------+------------------------+------------------------+--------------+
| client | client.crt             | ubuntu@xserver00       | 17a823c5a2b1 | 
+--------+------------------------+------------------------+--------------+

```

### Add the remote legacy LXD server to Incus
```shell
# remote lxc server ip : 10.10.10.21 
# set name show on incus as `xserver01`
incus remote add xserver01 10.10.10.21

# If get the error
Error: json: cannot unmarshal bool into Go struct field Server.config of type string

# On the LXC host, unset any token identification that might have been set up:
lxc config unset core.trust_password
```


### Reference

* [Connecting to legacy LXD server via remote](https://discuss.linuxcontainers.org/t/connecting-to-legacy-lxd-server-via-remote/19187/7)
* [Adding a LXC remote server to Incus Server](https://discuss.linuxcontainers.org/t/adding-a-lxc-remote-server-to-incus-server/19262/4)