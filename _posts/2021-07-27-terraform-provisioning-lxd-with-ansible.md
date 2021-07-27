---
layout : post
title : "Terraform Provisioning LXD with Ansible"
categories : [ansible, terraform, lxd]
published : true
---
**Terraform** is an open-source infrastructure-as-code (IaC) tool that has become the de facto solution for provisioning one aspect of those components. [1][1]

**Ansible** is another open-source tool that does software provisioning, configuration management and application deployments. In simple words, it takes over a newly created server instance and installs the required software based on a recipe book (called a playbook). Ansible works by using a playbook, which is a file containing a declarative description of our configuration state. [1][1]


### [Installing Terraform][1]
Install Terraform using the apt install command

```shell
$ lsb_release -cs
hirsute

# Repository Configuration
# configure your system to trust that HashiCorp key
$ curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -

# add the official HashiCorp repository to your system:
$ sudo apt-add-repository "deb [arch=$(dpkg --print-architecture)] \
  https://apt.releases.hashicorp.com $(lsb_release -cs) main"

# To install Terraform from the new repository:
$ sudo apt install terraform
...
After this operation, 78.4 MB of additional disk space will be used.
Get:1 https://apt.releases.hashicorp.com hirsute/main amd64 terraform amd64 1.0.3 [32.4 MB]
...
Setting up terraform (1.0.3) ...

$ terraform --version
Terraform v1.0.3
on linux_amd64

```

### Installing Ansible
Use the default Ubuntu repositories:

```shell
$ sudo apt install ansible

$ ansible --version
ansible 2.10.5
  config file = None
  ...
  ansible python module location = /usr/lib/python3/dist-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.9.5 (default, May 11 2021, 08:20:37) [GCC 10.3.0]

```



[1]: https://www.terraform.io/docs/cli/install/apt.html "APT Packages for Debian and Ubuntu"


[2]: https://victorops.com/blog/writing-ansible-playbooks-for-new-terraform-servers "writing-ansible-playbooks-for-new-terraform-servers"
