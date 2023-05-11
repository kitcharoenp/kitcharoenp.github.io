---
layout : post
title : "Use SSHFS Mounting a Remote Filesystem"
categories : []
published : false
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

### [Permanently Mounting the Remote Filesystem](https://adamtheautomator.com/sshfs-mount/)

edit **/etc/fstab:**
```
ubuntu@remote_server:/opt/remote_localfiles  /var/www/../runtime/uploads/localfiles fuse.sshfs identityfile=/home/ubuntu/.ssh/id_rsa,allow_other,_netdev 0 0
```


### Reference
 * [digitalocean](https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh)
 * [adamtheautomator](https://adamtheautomator.com/sshfs-mount/)


### Error

```shell
localfiles /home/oamp00/crm_localfiles
fuse: mountpoint is not empty
fuse: e
```

 sudo sshfs -o allow_other,default_permissions ubuntu@10.10.10.122:/opt/crm_localfiles /home/oamp00/crm_localfiles

 sshfs -o uid=$(id -u www-data) -o gid=$(id -g www-data) user@host:/path mountpoint

 sudo sshfs -o uid=33,gid=33,allow_other root@192.168.0.100:/var/www/a /home/gruz/debian

 sshfs -o uid=$(id -u www-data), gid=$(id -g www-data),allow_other ubuntu@192.168.0.100:/var/www/a /home/gruz/debian


 https://unix.stackexchange.com/questions/361930/sshfs-mount-files-folder-are-created-as-root-disregarding-uid-gid-options
 https://unix.stackexchange.com/questions/387645/why-cant-www-data-access-an-sshfs-mount
 https://unix.stackexchange.com/questions/59685/sshfs-mount-sudo-gets-permission-denied