---
layout: post
title: "Linux containers"
published: false
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

[1]: https://lxd.readthedocs.io/en/latest/storage/ "LXD"
[2]: https://stgraber.org/2016/04/12/lxd-2-0-remote-hosts-and-container-migration-612/ "LXD remote container"
