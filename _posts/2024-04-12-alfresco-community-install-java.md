---
layout : post
title : "Alfresco Community 23.x Install Java (OpenJDK 17.x.x)"
categories : [alfreco]
published : true
---
### Supported platforms
[Here](https://docs.alfresco.com/content-services/latest/support/) is a list of the individual components that have been through the complete Alfresco Quality Assurance and Certification activities for Alfresco Content Services 23.x. 

### Step 1: Update Ubuntu before OpenJDK 17 Installation

```shell
sudo apt update
sudo apt upgrade
```

### Install OpenJDK 17 with Ubuntu Repository

```shell
# Search for available packages 
apt-cache search openjdk | grep openjdk-17

# Output
openjdk-17-dbg - Java runtime based on OpenJDK (debugging symbols)
openjdk-17-demo - Java runtime based on OpenJDK (demos and examples)
openjdk-17-doc - OpenJDK Development Kit (JDK) documentation
openjdk-17-jdk - OpenJDK Development Kit (JDK)
openjdk-17-jdk-headless - OpenJDK Development Kit (JDK) (headless)
openjdk-17-jre - OpenJDK Java runtime, using Hotspot JIT
openjdk-17-jre-headless - OpenJDK Java runtime, using Hotspot JIT (headless)
openjdk-17-jre-zero - Alternative JVM for OpenJDK, using Zero
openjdk-17-source - OpenJDK Development Kit (JDK) source files

# Install
sudo apt install openjdk-17-jdk

# Check what is the default java version
java --version

# Output
openjdk 17.0.10 2024-01-16
OpenJDK Runtime Environment (build 17.0.10+7-Ubuntu-122.04.1)
OpenJDK 64-Bit Server VM (build 17.0.10+7-Ubuntu-122.04.1, mixed mode, sharing)

```

### Switching Alternative Java Version

After installing Java, you may want to check for newer versions and switch to them if necessary. Run the following command: 

```shell
# Switch version
sudo update-alternatives --config java

# Output: If you have only one alternative version
There is only one alternative in link group java (providing /usr/bin/java): /usr/lib/jvm/java-17-openjdk-amd64/bin/java
Nothing to configure.
```

### Set `JAVA_HOME` Environment Variable
Example `~/.profile` after set `JAVA_HOME` environment variable

```shell
# .profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
. ~/.bashrc
fi

# User specific environment and startup programs
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64

export ALF_HOME=/usr/local/alfresco-community23x
export SOLR_HOME=/usr/local/alfresco-search-services

PATH=$PATH:$HOME/.local/bin:$HOME/bin:$ALF_HOME:$SOLR_HOME

export PATH
```


### Reference

* [Install OpenJDK 17 on Ubuntu 22.04](https://www.linuxcapable.com/how-to-install-openjdk-17-on-ubuntu-linux/)

* [Installing Java](https://javaworld-abhinav.blogspot.com/2021/06/setup-acs70-ass201-and-transformation-service.html#setup-java11-centos)
