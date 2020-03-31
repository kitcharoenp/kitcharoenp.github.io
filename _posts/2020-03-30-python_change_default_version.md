---
layout: post
title: "Python : Change the default version"
published: true
categories: [python]
---

### Install Python 3
```shell
sudo apt update

sudo apt install python3
```

### [Configure python3 as default interpreter][1]
```shell
# configure an alias
echo "alias python=python3" >> ~/.bash_aliases

# updated information
source ~/.bash_aliases

# show version
python --version
Python 3.7.5

```

### Configure pip3
```shell
#install latest version of PIP for Python3
sudo apt install python3-pip

# configure an alias
echo "alias pip=pip3" >> ~/.bash_aliases

# updated information
source ~/.bash_aliases

pip --version
pip 18.1 from /usr/lib/python3/dist-packages/pip (python 3.7)
```


[1]: https://garywoodfine.com/configure-python-3-4-default-ubuntu-14-04-2/ "Configure Python 3 as default on Ubuntu"
