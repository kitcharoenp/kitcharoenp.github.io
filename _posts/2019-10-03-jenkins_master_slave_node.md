---
layout: post
title: "Jenkins Master and Slave"
published: false
categories: [jenkins]
---

```shell
root@jenkins:~# su jenkins
jenkins@jenkins:/root$ cd
jenkins@jenkins:~$ pwd
/var/lib/jenkins
jenkins@jenkins:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/var/lib/jenkins/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /var/lib/jenkins/.ssh/id_rsa.
Your public key has been saved in /var/lib/jenkins/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:YHE1EA2+XrSbK7GMEPDcxJaqse/N/iX3wzcTrUxw4Ks jenkins@jenkins
The key's randomart image is:
+---[RSA 2048]----+
|     ...=*o      |
|  .   =+  ...    |
|   + =o . .. .   |
|  . =... o .o .  |
|   + .  S o  + . |
|  o .  ... o. o .|
|   . . ooo=o o o |
|    .o. ++Eoo *  |
|   ...+.... .o o |
+----[SHA256]-----+

jenkins@jenkins:~$ ls -lah ~/.ssh/
total 17K
drwx------  2 jenkins jenkins    4 Oct  3 09:31 .
drwxr-xr-x 16 jenkins jenkins   32 Oct  3 09:23 ..
-rw-------  1 jenkins jenkins 1.7K Oct  3 09:31 id_rsa
-rw-r--r--  1 jenkins jenkins  397 Oct  3 09:31 id_rsa.pub
jenkins@jenkins:~$
```

```shell
jenkins@jenkins:~$ ssh-copy-id jenkins@10.254.162.157
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/var/lib/jenkins/.ssh/id_rsa.pub"
The authenticity of host '10.254.162.157 (10.254.162.157)' can't be established.
ECDSA key fingerprint is SHA256:kdMqE+Uq9tVL73BTd+88EasCdGA8DLmb530HbTBTQ9Q.
Are you sure you want to continue connecting (yes/no)? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
jenkins@10.254.162.157's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'jenkins@10.254.162.157'"
and check to make sure that only the key(s) you wanted were added.

jenkins@jenkins:~$

```


### Reference
* [Template for Jenkins Jobs for PHP Projects](http://jenkins-php.org/)
* [How to Setup Jenkins Master and Slave on Ubuntu](https://www.howtoforge.com/tutorial/ubuntu-jenkins-master-slave/#step-configure-jenkins-master-credentials)
* [linter-phpcs](https://atom.io/packages/linter-phpcs)
* [Laravel Unit Testing with Jenkins](http://lifewithlaravel.blogspot.com/2016/07/laravel-unit-testing-with-jenkins.html)
