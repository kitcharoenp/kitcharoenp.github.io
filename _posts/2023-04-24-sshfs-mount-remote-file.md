---
layout : post
title : "Use SSHFS Mounting a Remote Filesystem"
categories : []
published : true
---
### Create a container
```shell
$ lxc launch ubuntu:22.04 fileserver
```

**remote_server :**
```shell
# create a directory for store files
$ sudo mkdir /opt/remote_localfiles
# change owner
$ sudo chown -R ubuntu:ubuntu /opt/remote_localfiles
# change directory permission
$ chmod o+w /opt/remote_localfiles
```

### Install `sshfs` package
**local_server :**
```shell
$ sudo apt install sshfs

# create a directory for mount point
$ mkdir /var/www/../runtime/uploads/localfiles

# copied your SSH key to the remote system
$ ssh-copy-id ubuntu@remote_server
```

### Mount a remote directory
```shell
$ sudo sshfs -o allow_other,default_permissions ubuntu@remote_server:/opt/remote_localfiles/ /var/www/../runtime/uploads/localfiles
```

### [Permanently Mounting the Remote Filesystem](https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh#step-3-permanently-mounting-the-remote-filesystem)

edit **/etc/fstab:**
```
ubuntu@remote_server:/opt/remote_localfiles /var/www/../runtime/uploads/localfiles fuse.sshfs noauto,x-systemd.automount,_netdev,reconnect,identityfile=/home/ubuntu/.ssh/id_rsa,allow_other,default_permissions 0 0
```


### Reference
 * [digitalocean](https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh)