---
layout: post
title: "Alfresco : Maven SDK"
published: false
categories: [alfresco]
---
Notes while reading :
* [Getting Started with the Alfresco Maven SDK](https://ecmarchitect.com/alfresco-developer-series)
* [Alfresco SDK 4.0](https://github.com/Alfresco/alfresco-sdk)

### [Apache Maven](https://maven.apache.org/)
> Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model (POM), Maven can manage a project's build, reporting and documentation from a central piece of information.

>It has many features, but the primary time-saving feature is its ability to understand the dependencies your project relies on.

### [Alfresco SDK](https://github.com/Alfresco/alfresco-sdk)
> Alfresco SDK 4.0 is a Maven based development kit that provides an easy to use approach to developing applications and extensions for Alfresco. With this SDK you can develop, package, test, run, document and release your Alfresco extension project.

> It consists of a project template (an "archetype" in Maven parlance) and some built-in smarts that make Maven understand how to create AMPs and deploy them to Alfresco.


### Alfresco Module Package (AMP)
> An AMP is a ZIP file with a folder structure that follows a specific convention. AMP files are used to make it easy to share and deploy customizations to the Alfresco platform.


## Prerequisites

### Java Development Kit (JDK) - Version 11
Install Java (the default JDK):
```shell
$ sudo apt install default-jre
# check the version
$ java -version
openjdk version "11.0.4" 2019-07-16
OpenJDK Runtime Environment (build 11.0.4+11-post-Ubuntu-1ubuntu218.04.3)
OpenJDK 64-Bit Server VM (build 11.0.4+11-post-Ubuntu-1ubuntu218.04.3, mixed mode, sharing)
```
Make sure JAVA_HOME is setup correctly
```shell
$ env|grep JAVA_HOME
JAVA_HOME=/usr/lib/jvm/default-java

```

### [Apache Maven](https://maven.apache.org/install.html)
Alfresco SDK 4.0 requires Maven 3.3.0+
```shell
$ cd /tmp

# download
$ wget https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz

# extract
$ tar -xvzf apache-maven-3.6.2-bin.tar.gz

# copy
$ sudo cp -r apache-maven-3.6.2 /opt/maven
```

Set up the environment variables
```shell
$ sudo nano /etc/profile.d/maven.sh

# Add the following line
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```

Load the environment
```shell
$ sudo chmod 755 /etc/profile.d/maven.sh
$ source /etc/profile.d/maven.sh

# check the Maven installation
$ mvn -version
Apache Maven 3.6.2 (40f52333136460af0dc0d7232c0dc0bcf0d9e117; 2019-08-27T15:06:16Z)
Maven home: /opt/maven
Java version: 11.0.4, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-70-generic", arch: "amd64", family: "unix"
```


### Docker
```
$ sudo apt install docker.io docker-compose
```
Verify the installation
```
$ docker -v
Docker version 18.09.7, build 2d0083d
$ docker-compose -v
docker-compose version 1.17.1, build unknown
```

### [Generate your project from the archetypes](https://github.com/Alfresco/alfresco-sdk/blob/master/docs/getting-started.md#generate-your-project-from-the-archetypes)

These [`archetypes`](https://github.com/Alfresco/alfresco-sdk/blob/master/docs/mvn-archetypes.md) are available during the creation of a brand new project. In short, a Maven archetype is a project templating toolkit.

Create an empty directory -> refer to it as `$TUTORIAL_HOME`
```shell
$ sudo mkdir /opt/alf_tutorial
$ chown -R ubuntu:ubuntu /opt/alf_tutorial
$ cd /opt/alf_tutorial
$ mvn archetype:generate -Dfilter=org.alfresco:

```

Choose an project template (`"archetype"`) and specify the version of the archetype (4.0.0)
choose `"alfresco-allinone-archetype"` so type 2
```shell
Choose a number or apply filter (format: [groupId:]artifactId, case sensitive contains): : 2
Choose org.alfresco.maven.archetype:alfresco-allinone-archetype version:
1: 2.0.0-beta-1
2: 2.0.0-beta-2
3: 2.0.0-beta-3
...
11: 3.1.0
12: 4.0.0-beta-1
13: 4.0.0
Choose a number: 13:
Downloading from central: https://repo.maven.apache.org/maven2/org/alfresco/maven/archetype/alfresco-allinone-archetype/4.0.0/alfresco-allinone-archetype-4.0.0.pom
```

Specify `groupId` and `artifactId`
```shell
(65 kB at 88 kB/s)
Define value for property 'groupId': com.someco
Define value for property 'artifactId': maven-sdk-tutorial
[INFO] Using property: version = 1.0-SNAPSHOT
Define value for property 'package' com.someco: :
```

Confirm properties configuration.
change something you can specify "N" and then make changes or you can enter "Y" to continue.
```shell
Confirm properties configuration:
groupId: com.someco
artifactId: maven-sdk-tutorial
version: 1.0-SNAPSHOT
package: com.someco
 Y: : Y
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Archetype: alfresco-allinone-archetype:4.0.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.someco
[INFO] Parameter: artifactId, Value: maven-sdk-tutorial
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
...
[INFO] Project created from Archetype in dir: /opt/alf_tutorial/maven-sdk-tutorial
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  09:44 min
[INFO] Finished at: 2019-11-25T03:48:41Z
[INFO] ------------------------------------------------------------------------
```

Directory structure
```shell
$ ls maven-sdk-tutorial/
README.md                             maven-sdk-tutorial-platform-docker  run.bat
docker                                maven-sdk-tutorial-share            run.sh
maven-sdk-tutorial-integration-tests  maven-sdk-tutorial-share-docker
maven-sdk-tutorial-platform           pom.xml
```

* `pom.xml` : This XML file contains information about the project and configuration details used by Apache Maven to build the project.
### Run
`run.sh` scripts are convenience scripts for you to quickly compile / test / run your project.

`Usage: ./run.sh {build_start|build_start_it_supported|start|stop|purge|tail|reload_share|reload_acs|build_test|test}`
```
$  cd maven-sdk-tutorial
$ sudo ./run.sh build_start
```

> If you watch the output, you'll see that Maven is downloading everything it needs to compile the project, creating an AMP, deploying the AMP to the Alfresco WAR, deploying the Alfresco WAR to the embedded Tomcat server, and starting the server up.

### Failed
```
Failed to read artifact descriptor for org.slf4j:slf4j-api:jar:1.7.6: Could not transfer artifact org.slf4j:slf4j-parent:pom:1.7.6 from/to alfresco-private-repository (https://artifacts.alfresco.com/nexus/content/groups/private): Not authorized -> [Help 1]
```

### [DependencyResolutionException](https://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException)
This error generally occurs when Maven could not download dependencies

After correcting the problems, you can resume the build with the command
```
mvn <args> -rf :maven-sdk-tutorial-platform
```
