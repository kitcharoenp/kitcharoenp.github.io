---
layout : post
title : "Install Ansible on Ubuntu 20.04"
categories : [ansible]
published : true
---

### [Installing Ansible][1]
Use the default Ubuntu repositories:

```shell
$ lsb_release -cs
hirsute

$ sudo apt update

$ sudo apt install ansible

$ ansible --version
ansible 2.10.5
  config file = None
  ...
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.9.5 (default, May 11 2021, 08:20:37) [GCC 10.3.0]

```

### [Setting Up the Inventory File][1]
The **inventory file** contains information about the hosts you’ll manage with Ansible.

**Note:** Although Ansible typically creates a default inventory file
at `etc/ansible/hosts`, you are **free to create inventory files in
any location** that better suits your needs. In this case, you’ll
need to provide the path to your custom inventory file with the `-i`
parameter when running Ansible commands and playbooks.

**~/ansible_prj/ansible-mylocal/hosts:**
```
[servers]
repl1 ansible_host=10.14.193.204
repl2 ansible_host=10.14.193.105


[all:vars]
ansible_python_interpreter=/usr/bin/python3
```
The `all:vars` subgroup sets the `ansible_python_interpreter` host parameter that will be valid for all hosts included in this inventory.

```shell
$ ansible-inventory --help

    -i INVENTORY, --inventory INVENTORY, --inventory-file INVENTORY
                          specify inventory host path or comma separated host
                          list. --inventory-file is deprecated
    -y, --yaml            Use YAML format instead of default JSON,
    --list                Output all hosts info, works as inventory script


# check your inventory
$ ansible-inventory -i ~/ansible_prj/ansible-mylocal/hosts --list -y
all:
  children:
    servers:
      hosts:
        repl1:
          ansible_host: 10.14.193.204
          ansible_python_interpreter: /usr/bin/python3
        repl2:
          ansible_host: 10.14.193.105
          ansible_python_interpreter: /usr/bin/python3
    ungrouped: {}
```

### Testing Connection

```shell
# Testing Connection with valid SSH credentials
$ ansible all -m ping  -u ubuntu -i ~/ansible_prj/ansible-mylocal/hosts
repl2 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
repl1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}

# specify multiple hosts
$ ansible repl1:repl2 -m ping  -u ubuntu -i ~/ansible_prj/ansible-mylocal/hosts


$ ansible all -a "df -h" -u ubuntu -i ~/ansible_prj/ansible-mylocal/hosts
repl2 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/loop22      88G   30G   58G  35% /
none            492K  4.0K  488K   1% /dev
udev            3.8G     0  3.8G   0% /dev/tty
tmpfs           100K     0  100K   0% /dev/lxd
tmpfs           100K     0  100K   0% /dev/.lxd-mounts
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           783M  204K  783M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           783M     0  783M   0% /run/user/1000
repl1 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/loop22      88G   30G   58G  35% /
none            492K  4.0K  488K   1% /dev
udev            3.8G     0  3.8G   0% /dev/tty
tmpfs           100K     0  100K   0% /dev/lxd
tmpfs           100K     0  100K   0% /dev/.lxd-mounts
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           783M  204K  783M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           783M     0  783M   0% /run/user/1000
```


[1]: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-20-04 "Ansible on Ubuntu 20.04"
