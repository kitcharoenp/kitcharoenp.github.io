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
Make sure **JAVA_HOME** is setup correctly
```shell
$ env|grep JAVA_HOME
JAVA_HOME=/usr/lib/jvm/default-java

```

### [Apache Maven](https://maven.apache.org/install.html)
Alfresco SDK 4.0 requires Maven 3.3.0+

#### Installing with `apt`
Installing Maven on Ubuntu using `apt`.
```shell
sudo apt install maven
```

#### Install the Latest Release
```shell
$ cd /tmp

# download
$ wget https://www-eu.apache.org/dist/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz

# extract
$ tar -xvzf apache-maven-3.6.2-bin.tar.gz

# copy
$ sudo cp -r apache-maven-3.6.2 /opt/maven
```

Setup environment variables
```shell
$ sudo nano /etc/profile.d/maven.sh
```
Paste the following configuration
```shell
# Add the following line
export JAVA_HOME=/usr/lib/jvm/default-java
export M2_HOME=/opt/maven
export MAVEN_HOME=/opt/maven
export PATH=${M2_HOME}/bin:${PATH}
```
Save and close the file.

Load the environment
```shell
$ sudo chmod 755 /etc/profile.d/maven.sh
$ source /etc/profile.d/maven.sh
```

#### Check the Maven installation
```shell
$ mvn -version
Apache Maven 3.6.2 (40f52333136460af0dc0d7232c0dc0bcf0d9e117; 2019-08-27T15:06:16Z)
Maven home: /opt/maven
Java version: 11.0.4, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en, platform encoding: UTF-8
OS name: "linux", version: "4.15.0-70-generic", arch: "amd64", family: "unix"
```


### Docker

```shell
$ sudo apt install docker.io docker-compose
```

#### [Docker inside a LXD](https://lxd.readthedocs.io/en/latest/#how-can-i-run-docker-inside-a-lxd-container)

In order to run Docker inside a LXD container the security.nesting property of the container should be set to true.
```
lxc config set <container> security.nesting true
```

#### Verify the installation
```shell
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

Choose [**"alfresco-allinone-archetype"**](https://github.com/Alfresco/alfresco-sdk/blob/7baf3fb0a19082e128f696e518fc9f1d744fd559/docs/working-with-generated-projects/working-with-aio.md) so type 2:
```
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

Specify `groupId` and `artifactId`:
```shell
(65 kB at 88 kB/s)
Define value for property 'groupId': com.someco
Define value for property 'artifactId': maven-sdk-tutorial
[INFO] Using property: version = 1.0-SNAPSHOT
Define value for property 'package' com.someco: :
```

Confirm properties configuration.
change something you can specify "N" and then make changes or you can enter "Y" to continue.
```
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


### [Run script](https://github.com/Alfresco/alfresco-sdk/blob/7baf3fb0a19082e128f696e518fc9f1d744fd559/docs/working-with-generated-projects/README.md#run-script)
> All the projects generated using the Alfresco SDK 4.0 archetypes provide a utility script to work with the project. This script is run.sh for Unix systems.

> `build_start`: Build the whole project, recreate the ACS and Share docker images, start the dockerised environment composed by ACS, Share, ASS and PostgreSQL and tail the logs of all the containers.

`Usage: ./run.sh {build_start|build_start_it_supported|start|stop|purge|tail|reload_share|reload_acs|build_test|test}`
```
$  cd maven-sdk-tutorial
$ ./run.sh build_start

...
[INFO] ------------------------------------------------------------------------
[INFO] Reactor Summary for AIO - SDK 4.0 1.0-SNAPSHOT:
[INFO]
[INFO] AIO - SDK 4.0 ...................................... SUCCESS [  0.577 s]
[INFO] Alfresco Platform/Repository JAR Module ............ SUCCESS [  4.030 s]
[INFO] Alfresco Share JAR Module .......................... SUCCESS [  3.405 s]
[INFO] Integration Tests Module ........................... SUCCESS [  0.806 s]
[INFO] Alfresco Platform/Repository Docker Module ......... SUCCESS [05:37 min]
[INFO] Alfresco Share Docker Module ....................... SUCCESS [  0.119 s]
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  05:46 min
[INFO] Finished at: 2019-11-25T10:03:36Z
[INFO] ------------------------------------------------------------------------
...
Successfully built d44b489edffd
Successfully tagged alfresco-content-services-maven-sdk-tutorial:development
Creating docker_maven-sdk-tutorial-share_1 ...
Creating docker_maven-sdk-tutorial-postgres_1 ...
Creating docker_maven-sdk-tutorial-ass_1 ...
Creating docker_maven-sdk-tutorial-share_1
Creating docker_maven-sdk-tutorial-ass_1
Creating docker_maven-sdk-tutorial-postgres_1

```

> If you watch the output, you'll see that Maven is downloading everything it needs to compile the project, creating an AMP, deploying the AMP to the Alfresco WAR, deploying the Alfresco WAR to the embedded Tomcat server, and starting the server up.

This will build the following Docker image:
```
$ docker image ls
REPOSITORY                                       TAG                 IMAGE ID            CREATED             SIZE
alfresco-content-services-maven-sdk-tutorial     development         d44b489edffd        About an hour ago   2.07GB
alfresco-share-maven-sdk-tutorial                development         fd3c108d8c35        2 hours ago         749MB
...
postgres                                         9.6                 43841c51ff71        3 days ago          250MB
alfresco/alfresco-content-repository-community   6.1.2-ga            6edaf25aded1        10 months ago       2.05GB
alfresco/alfresco-share                          6.1.0-RC3           31eb8aebeecc        10 months ago       749MB
alfresco/alfresco-search-services                1.2.0               484288307a19        15 months ago       864MB
```

List Running Docker Containers
```shell
$ docker ps
CONTAINER ID        IMAGE                                                    COMMAND                  CREATED             STATUS              PORTS                                                      NAMES
3aa292dd6f17        alfresco-content-services-alf-mvn-tutorial:development   "catalina.sh run -se…"   35 minutes ago      Up 35 minutes       0.0.0.0:8080->8080/tcp, 0.0.0.0:8888->8888/tcp             docker_alf-mvn-tutorial-acs_1
5756b83a0c9a        alfresco-share-alf-mvn-tutorial:development              "/usr/local/tomcat/s…"   36 minutes ago      Up 35 minutes       8000/tcp, 0.0.0.0:8180->8080/tcp, 0.0.0.0:9898->8888/tcp   docker_alf-mvn-tutorial-share_1
38f9c5d31262        postgres:9.6                                             "docker-entrypoint.s…"   36 minutes ago      Up 35 minutes       0.0.0.0:5555->5432/tcp                                     docker_alf-mvn-tutorial-postgres_1
4f6ca8f966e7        alfresco/alfresco-search-services:1.2.0                  "/bin/sh -c '$DIST_D…"   36 minutes ago      Up 35 minutes       0.0.0.0:8983->8983/tcp                                     docker_alf-mvn-tutorial-ass_1
```

You should be able to go to:
```
http://localhost:8080/alfresco
# verify that you can log in, go to the Alfresco web script console, which is:
http://localhost:8080/alfresco/s/index
```

Shutdown all of the Docker containers.
```shell
$ ./run.sh stop
Stopping docker_alf-mvn-tutorial-acs_1      ... done
Stopping docker_alf-mvn-tutorial-share_1    ... done
Stopping docker_alf-mvn-tutorial-postgres_1 ... done
Stopping docker_alf-mvn-tutorial-ass_1      ... done
Removing docker_alf-mvn-tutorial-acs_1      ... done
Removing docker_alf-mvn-tutorial-share_1    ... done
Removing docker_alf-mvn-tutorial-postgres_1 ... done
Removing docker_alf-mvn-tutorial-ass_1      ... done
Removing network docker_default
```

 `target` directory, you'll see that the build also created a "Share/Alfresco tier" JAR
 ```shell
 $ ls  alf-mvn-tutorial-platform/target/
alf-mvn-tutorial-platform-1.0-SNAPSHOT.jar  generated-sources       maven-archiver  surefire-reports
classes                                     generated-test-sources  maven-status    test-classes
# share
 $ ls  alf-mvn-tutorial-share/target/
 alf-mvn-tutorial-share-1.0-SNAPSHOT.jar  classes  maven-archiver  maven-status
 ```
It simplifies things to use **AMPs** all of the time and to never use JARs.
To change your project to produce AMPs, edit the `your_project/pom.xml`. Search for the `"maven-assembly-plugin"` and uncomment it. Now when you run `mvn install -DskipTests` you'll see that an **AMP** gets produced in the platform and share target directories.

*your_project/pom.xml:*
```
<!--
    Build an AMP if 3rd party libs are needed by the extensions
    JARs are the default artifact produced in your modules, if you want to build an amp for each module
    you have to enable this plugin and inspect the src/main/assembly.xml file if you want to customize
    the layout of your AMP. The end result is that Maven will produce both a JAR file and an AMP with your
    module.
-->

<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>2.6</version>
    <executions>
        <execution>
            <id>build-amp-file</id>
            <phase>package</phase>
            <goals>
                <goal>single</goal>
            </goals>
            <configuration>
                <appendAssemblyId>false</appendAssemblyId>
                <descriptor>src/main/assembly/amp.xml</descriptor>
            </configuration>
        </execution>
    </executions>
    <dependencies>
        <dependency>
            <groupId>org.alfresco.maven.plugin</groupId>
            <artifactId>alfresco-maven-plugin</artifactId>
            <version>${alfresco.sdk.version}</version>
        </dependency>
    </dependencies>
</plugin>
```

```
$ mvn install -DskipTests
```
By default, Maven will automatically run the unit tests and integration tests in your project unless you include -DskipTests.

### Unit & Integration Tests
To run integration tests using SDK 4.0, first start up the server using run.sh build_start. Once it is running, do run.sh test to run the integration tests.

You can greatly speed up your test-fix cycle by using something like JRebel. With JRebel, after you launch the Docker containers, you can make changes to your integration test classes, hot-deploy them to the running server, then re-run your tests, all without having to restart.


###Cleaning Up

If you want to delete all of the compiled artifacts that Maven created and start fresh you can run:
```
mvn clean
```
If you also want to delete the Docker containers and volumes that were created by running the test Alfresco server, you can run ./run.sh purge.

## Troubleshooting

### Permission Docker daemon
#### Problem
Got permission denied while trying to connect to the Docker daemon socket.
```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.39/volumes/create: dial unix /var/run/docker.sock: connect: permission denied
```
#### Solution
Run Docker as a non-root user
```shell
$ sudo usermod -a -G docker $USER
```

#### [DependencyResolutionException](https://cwiki.apache.org/confluence/display/MAVEN/DependencyResolutionException)

#### Problem
This error generally occurs when Maven could not download dependencies.

#### Solution
Try run `./run.sh build_start` until install all dependencies completed.


```
ERROR: for maven-sdk-tutorial-acs  UnixHTTPConnectionPool(host='localhost', port=None): Read timed out. (read timeout=60)
ERROR: An HTTP request took too long to complete. Retry with --verbose to obtain debug information.
```

[Working with generated projects](https://github.com/Alfresco/alfresco-sdk/blob/7baf3fb0a19082e128f696e518fc9f1d744fd559/docs/working-with-generated-projects/README.md)

[Working with a Share project](https://github.com/Alfresco/alfresco-sdk/blob/7baf3fb0a19082e128f696e518fc9f1d744fd559/docs/working-with-generated-projects/working-with-share.md)
