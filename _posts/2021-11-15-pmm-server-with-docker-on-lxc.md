---
layout : post
title : "PMM Server with Docker on LXC container"
categories : [pmm, docker, lxc, mysql, mariadb]
published : true
---
## [Install Docker](https://phoenixnap.com/kb/install-docker-on-ubuntu-20-04)

```shell
# Downloading Dependencies
sudo apt-get install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

# Adding Docker’s GPG Key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Installing the Docker Repository
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu  $(lsb_release -cs)  stable"

# Installing the Latest Docker
sudo apt update

sudo apt-get install docker-ce docker-ce-cli containerd.io

# Verifying Docker Installation
docker --version

# Enable Docker Service 
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker

# Add your user to the docker group
sudo usermod -aG docker $USER

```


### [Set up PMM Server with Docker](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/server/docker.html)

```shell
# Pull the image.
docker pull percona/pmm-server:2

# Create a persistent data container.
docker create --volume /srv \
--name pmm-data \
percona/pmm-server:2 /bin/true

# Run the image.
docker run --detach --restart always \
--publish 443:443 \
--volumes-from pmm-data \
--name pmm-server \
percona/pmm-server:2
```

In a web browser, visit https://localhost:443 to see the PMM user interface.

### [Set up PMM Client](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/client/index.html#package-manager)

Install PMM Client with `package-manager`

```shell
# Configure repositories.
wget https://repo.percona.com/apt/percona-release_latest.generic_all.deb
sudo dpkg -i percona-release_latest.generic_all.deb

# Install the PMM Client package.
sudo apt update
sudo apt install -y pmm2-client
```

**Check**
```shell
$ pmm-admin --version
ProjectName: pmm-admin
Version: 2.23.0
PMMVersion: 2.23.0
Timestamp: 2021-10-19 09:56:50 (UTC)
FullCommit: 2af1c987f304a3fa375af0986b6af6a0390d41bd
```

### [Register Node](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/client/index.html#register)

```shell
$ sudo pmm-admin config --server-insecure-tls --server-url=https://admin:admin@X.X.X.X:443 Y.Y.Y.Y generic mynode
```
* `X.X.X.X` : is the address of your PMM Server.
* `Y.Y.Y.Y` : is the address of your PMM Client Node.
* `generic` : type
* `mynode` : node name

### [Add MySQL services](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/client/mysql.html)

```sql
-- creates a database user with name pmm, password pass
CREATE USER 'pmm'@'127.0.0.1' IDENTIFIED BY 'pass' WITH MAX_USER_CONNECTIONS 10;

-- grant permissions 
GRANT SELECT, PROCESS, SUPER, REPLICATION CLIENT, RELOAD ON *.* TO 'pmm'@'127.0.0.1';
```

### [Create a database account for PMM](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/client/mysql.html#create-a-database-account-for-pmm)
Use [`Performance Schema`](https://mariadb.com/kb/en/performance-schema-overview/) as source of matrics

**Configuration file: /etc/mysql/my.cnf**
```
performance_schema=ON
innodb_monitor_enable=all

performance-schema-instrument='statement/%=ON'
performance-schema-consumer-statements-digest=ON
performance-schema-consumer-events-stages-current=ON
performance-schema-consumer-events-stages-history=ON
performance-schema-consumer-events-stages-history-long=ON
```
### [Add service](https://www.percona.com/doc/percona-monitoring-and-management/2.x/setting-up/client/mysql.html#add-service)

```shell
pmm-admin add mysql --query-source=perfschema --username=pmm --password=pass REPL1041
```
* **MYSQL_NODE** : name show on pmmserver

## Troubleshooting

### OCI runtime create failed: container_linux.go:380:

```
docker: Error response from daemon: OCI runtime create failed: container_linux.go:380: starting container process caused: process_linux.go:545: container init caused: rootfs_linux.go:76: mounting "proc" to rootfs at "/proc" caused: mount through procfd: permission denied: unknown
```

**[Solution](https://stackoverflow.com/questions/46645910/docker-rootfs-linux-go-permission-denied-when-mounting-proc)**

```
lxc stop docker_container_name
lxc config set docker_container_name security.nesting true
lxc start docker_container_name
```
