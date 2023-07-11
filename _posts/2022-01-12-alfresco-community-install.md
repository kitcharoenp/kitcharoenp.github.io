---
layout : post
title : "Install Alfresco Community Edition using Docker Compose"
categories : [docker]
published : false
---
Note from :   [Install using Docker Compose](https://docs.alfresco.com/content-services/community/install/containers/docker-compose/)

To deploy Community Edition using Docker Compose, **download and install Docker**.

1. Add your user to the docker group
    ```shell
    sudo usermod -aG docker $USER
    ```

2. Clone the project locally
    ```shell
    git clone https://github.com/Alfresco/acs-deployment.git
    cd acs-deployment/docker-compose
    ```

3. Deploy Community Edition
    ```shell
    docker-compose -f community-docker-compose.yml up
    ```

4. Wait for the logs to complete, showing messages:
    ```
    [+] Running 8/8
    ⠿ Network docker-compose_default                 Created                                                                                        0.1s
    ...
    ⠿ Container docker-compose-share-1               Created                                                                                       17.9s
    ⠿ Container docker-compose-transform-core-aio-1  Created                                                                                       29.0s
    ⠿ Container docker-compose-proxy-1               Created 
    ....
    docker-compose-proxy-1               | 10.2.111.1 - - [13/Jan/2022:02:41:01 +0000] "GET /share/service/header/sites-menu/recent HTTP/1.1" 200 79 "http://10.2.111.245:8080/share/page/user/admin/dashboard" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
    docker-compose-proxy-1               | 10.2.111.1 - - [13/Jan/2022:02:41:15 +0000] "GET /share/service/modules/authenticated?noCache=1642041675918&a=user HTTP/1.1" 200 23 "http://10.2.111.245:8080/share/page/user/admin/dashboard" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/97.0.4692.71 Safari/537.36"
    ```

### [Error LXD container](https://stackoverflow.com/questions/46645910/docker-rootfs-linux-go-permission-denied-when-mounting-proc)

```
 ⠿ Container docker-compose-proxy-1               Created                                                                                        0.7s
Attaching to docker-compose-activemq-1, docker-compose-alfresco-1, docker-compose-postgres-1, docker-compose-proxy-1, docker-compose-share-1, docker-compose-solr6-1, docker-compose-transform-core-aio-1
Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: process_linux.go:545: container init caused: rootfs_linux.go:76: mounting "proc" to rootfs at "/proc" caused: mount through procfd: permission denied: unknown
```

**Fixed**

```shell
# Stop the container
lxc stop deploy-container

# Configure nesting on 
lxc config set deploy-container security.nesting true
```


### How to automatically start docker container
https://stackoverflow.com/questions/65933128/how-to-automatically-start-docker-container-on-windows-boot-wait-for-docker-to

https://hub.alfresco.com/t5/alfresco-content-services-forum/change-webpage-name-of-alfresco-share-change-link-address/td-p/231404