---
layout: post
title: "Ubuntu : Package Command"
categories: [server]
---
### List all installed packages
```shell
$ apt list --installed
```

### List of files installed from apt package
```shell
$ dpkg -L package_name
```

### List dependent packages (reverse dependencies)
```shell
$ apt-cache rdepends package_name
```
