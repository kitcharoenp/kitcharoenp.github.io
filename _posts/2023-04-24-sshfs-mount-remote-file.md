---
layout : post
title : "Use SSHFS Mounting a Remote Filesystem"
categories : [fileserver]
published : true
---
### Create a container
```shell
$ lxc launch ubuntu:22.04 fileserver
```

## **Remote Server : files_server**
```shell
# create a directory for store files
$ sudo mkdir /opt/remote_localfiles
# change owner
$ sudo chown -R ubuntu:ubuntu /opt/remote_localfiles
# change directory permission
$ chmod o+w /opt/remote_localfiles
```

## **Local Server :**
### Install `sshfs` package

```shell
$ sudo apt install sshfs

# create keys for use by SSH
$ ssh-keygen

# copied your SSH key to the remote system
$ ssh-copy-id -i /home/ubuntu/.ssh/id_rsa.pub ubuntu@remote_server

# create a directory for mount point
$ mkdir /var/www/../runtime/uploads/localfiles

# change mount directory permission
$ chmod o+w /var/www/../runtime/uploads/localfiles
```

### Mount a remote directory
```shell
$ sshfs -o allow_other,default_permissions ubuntu@remote_server:/opt/remote_localfiles/ /var/www/../runtime/uploads/localfiles
```

### [Permanently Mounting the Remote Filesystem](https://adamtheautomator.com/sshfs-mount/)

edit **/etc/fstab:**
```
ubuntu@remote_server:/opt/remote_localfiles  /var/www/../runtime/uploads/localfiles fuse.sshfs identityfile=/home/ubuntu/.ssh/id_rsa,allow_other,_netdev 0 0
```

**Validate fstab without rebooting**
```shell
# -a Mount all filesystems (of the given types) mentioned in fstab.
$ mount -a
```

**Show result**
```shell
$ df -h
Filesystem                               Size  Used Avail Use% Mounted on
..
tmpfs                                    100K     0  100K   0% /dev/lxd
tmpfs                                    100K     0  100K   0% /dev/.lxd-mounts
tmpfs                                     32G     0   32G   0% /dev/shm
..
snapfuse                                  54M   54M     0 100% /snap/snapd/19122
ubuntu@remote_server:/opt/remote_localfiles  371G  199G  172G  54% /var/www/../runtime/uploads/localfiles
tmpfs                                    6.3G     0  6.3G   0% /run/user/1000
```

### Error

* **option allow_other**
 ```
 fusermount: option allow_other only allowed if 'user_allow_other' is set in /etc/fuse.conf
 ```
 edit `/etc/fuse.conf` uncomment line `user_allow_other`

* **no write access**
```
fusermount: user has no write access to mountpoint ...
```
check mount directory permission

[Solution](https://askubuntu.com/questions/270164/trying-to-use-sshfs-on-ubuntu)

### Reference
 * [Digitalocean](https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh)
 * [Adamtheautomator](https://adamtheautomator.com/sshfs-mount/)



