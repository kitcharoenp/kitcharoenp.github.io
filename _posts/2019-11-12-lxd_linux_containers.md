---
layout: post
title: "Linux containers"
published: true
categories: [lxd]
---
Notes while reading :
* [Remote hosts and container migration][2]
* [Offcial Doc][1]

## Getting the packages
```
# ubuntu snap install lxd 4.0
snap install lxd --channel=4.0/stable
```

## [Launch an instance][3]
```
lxc launch ubuntu:20.04 ubuntu2004
```


### Chnage bridge IP address
```shell
$ lxc network edit lxdbr0

### This is a YAML representation of the network.
### Any line starting with a '# will be ignored.
###
### A network consists of a set of configuration items.
###
### An example would look like:
### name: lxdbr0
### config:
###   ipv4.address: 10.62.42.1/24
###   ipv4.nat: true
###   ipv6.address: fd00:56ad:9f7a:9800::1/64
###   ipv6.nat: true
### managed: true
### type: bridge
###
### Note that only the configuration can be changed.

config:
  ipv4.address: 10.10.10.12/24 # ip interface lxdbr0
  ipv4.nat: "true" #  to create iptables SNAT rule
  ipv6.address: none
description: ""
name: lxdbr0
type: bridge
used_by:
- /1.0/profiles/default
managed: true
status: Created
locations:
- none


```


## Interacting with remote hosts

### Adding a remote
You have two machines with LXD installed

* remote host: foo (remote host ip address : 1.2.3.4)

    ```
    lxc config set core.https_address [::]:8443
    lxc config set core.trust_password something-secure
    ```

* local machine

    ```
    lxc config set core.https_address [::]:8443
    # add “foo” to your local client
    lxc remote add foo 1.2.3.4
    ```    

### Listing containers
Listing running containers on a remote host (foo)

```
lxc list foo:
```

### Copying containers
Copying containers between remote host (foo:c1) and local (c2)

```
lxc copy foo:c1 c2

# copy of a container with snapshot
lxc copy foo:c1/snapshot1 c3
```

### Deletion protection for containers
```
# Set protection.delete
$ lxc config set c1 security.protection.delete true
$ lxc delete c1
Error: Container is protected

# Unset protection.delete
$ lxc config unset c1 security.protection.delete
$ lxc delete c1
```

### [Delete alias]

One is to setup an alias from `lxc delete` to `lxc delete -i`:

```
stgraber@castiana:~$ lxc alias add delete "delete -i"
stgraber@castiana:~$ lxc delete c1
Remove c1 (yes/no): 
```

[1]: https://lxd.readthedocs.io/en/latest/storage/ "LXD"

[2]: https://stgraber.org/2016/04/12/lxd-2-0-remote-hosts-and-container-migration-612/ "LXD remote container"

[3]: https://linuxcontainers.org/lxd/getting-started-cli/#launch-a-container "Launch an instance"

[4]: https://github.com/lxc/lxd/issues/4747#issuecomment-403031023 "alias add delete"
