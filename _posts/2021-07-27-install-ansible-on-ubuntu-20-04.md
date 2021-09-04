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

Using per-project inventory files is a good practice to avoid mixing servers when executing commands and playbooks. You can organize your servers into different groups and subgroups.  A host can be part of multiple groups.  \[[2]\]

**~/ansible_prj/ansible-mylocal/inventory:**
```
# Use an alias, include a variable named `ansible_host`
[production]
repl1 ansible_host=10.14.193.204 ansible_user=ubuntu

[production:vars]
ansible_user=ubuntu

[development]
repl2 ansible_host=10.14.193.105 ansible_user=ubuntu

[development:vars]
ansible_user=ubuntu

[repl_servers]
repl1
repl2

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

# change directory
$ cd ~/ansible_prj/ansible-mylocal/

# check your inventory
$ ansible-inventory -i inventory --list -y

all:
  children:
    development:
      hosts:
        repl2:
          ansible_host: 10.14.193.105
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ubuntu
    production:
      hosts:
        repl1:
          ansible_host: 10.14.193.204
          ansible_python_interpreter: /usr/bin/python3
          ansible_user: ubuntu
    repl_servers:
      hosts:
        repl1: {}
        repl2: {}
    ungrouped: {}

```

### Testing Connection

```shell
# Testing Connection with valid SSH credentials
$ ansible -i inventory all -m ping  -u ubuntu
repl1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
repl2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.14.193.105 port 22: No route to host",
    "unreachable": true
}


# specify multiple hosts and groups by separating them with colons
$ ansible -i inventory repl1:repl2 -m ping  -u ubuntu

repl2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.14.193.105 port 22: No route to host",
    "unreachable": true
}
repl1 | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```


### [Multiple Servers with Ansible Ad Hoc Commands][3]
* **playbooks** — which consist of collections of tasks that can be reused
* **ad hoc** —  commands are tasks that you don’t perform frequently, such as restarting a service or copying files.

### Running Bash Commands
When a module is not provided via the `-m` option, the command module is used by default to execute the specified command on the remote server(s).

This allows you to execute virtually any command that you could normally execute via an SSH terminal

```shell
# exec commands `df -h` on all hosts
$ ansible -i inventory all -a "df -h" -u ubuntu
repl1 | CHANGED | rc=0 >>
Filesystem      Size  Used Avail Use% Mounted on
/dev/loop25      88G   50G   37G  58% /
none            492K  4.0K  488K   1% /dev
udev            3.8G     0  3.8G   0% /dev/tty
tmpfs           100K     0  100K   0% /dev/lxd
tmpfs           100K     0  100K   0% /dev/.lxd-mounts
tmpfs           3.9G     0  3.9G   0% /dev/shm
tmpfs           783M  212K  783M   1% /run
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           783M     0  783M   0% /run/user/1000

repl2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host 10.14.193.105 port 22: No route to host",
    "unreachable": true
}
```


[1]: https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-ubuntu-20-04 "Ansible on Ubuntu 20.04"

[2]: https://www.digitalocean.com/community/tutorials/how-to-set-up-ansible-inventories "Set Up Ansible Inventories"

[3]: https://www.digitalocean.com/community/cheatsheets/how-to-manage-multiple-servers-with-ansible-ad-hoc-commands "Manage Multiple Servers with Ansible Ad Hoc Commands"
