---
layout : post
title : "Install a local Kubernetes with MicroK8s in LXD"
categories : [kubernetes, lxd]
published : false
---
Note from read ["MicroK8s"][1]

**MicroK8s:** Low-ops, minimal production Kubernetes,
for devs, cloud, clusters, workstations, Edge and IoT.

Microk8s is a lightweight, pure-upstream Kubernetes aiming to reduce the barriers to entry for K8s and cloud-native application development.

MicroK8s makes high availability resilient and self-healing with no administrative interventions required.

### [MicroK8s features][2]
*  Use Juju, JAAS or Helm to automate operations.
*  Use DNS, ingress, Flannel and Cilium for your project’s networking
*  Use GPU acceleration for your deep learning projects and, if you are an AI/ML geek, combine it with Kubeflow as your home edition HPC.
*  Use Istio or Linkerd for service mesh and MetalLb for load ballancing
*  Use KNative to write serverless applications

### What is high availability Kubernetes?
Three factors are necessary for a highly available Kubernetes cluster:
*  There must be **more than one worker node**. Since MicroK8s uses every node as a worker node, there is always more than one worker if there is more than one node in the cluster.

*  The **Kubernetes API services must be running on more than one node** so that losing a single node would not render the cluster inoperable.

*  The **cluster state must be in a reliable datastore.**

MicroK8s supports high availability using Dqlite as the datastore for cluster state. Using [Raft Consensus Algorithm][3], Dqlite automatically handles leader election, elects a replacement leader if one fails and ensures that the cluster state is always preserved

### MicroK8s in LXD

MicroK8s can also be installed inside an LXD container.

**[storage#recommended-setup:][4]** The two best options for use with LXD are **ZFS and btrfs.** They have about similar functionalities but ZFS is more reliable if available on your particular platform.

```shell
$ lxd --version
4.16

# storage info
$ lxc storage info default
info:
  description: ""
  driver: btrfs #
  name: default
  space used: 32.22GB
  total space: 94.00GB
```

### Add the new MicroK8s LXD profile

```shell
# Add the new MicroK8s LXD profile
$ lxc profile create microk8s
$ lxc profile list
+----------+---------------------+---------+
|   NAME   |     DESCRIPTION     | USED BY |
+----------+---------------------+---------+
| default  | Default LXD profile | 6       |
+----------+---------------------+---------+
| microk8s |                     | 0       |
+----------+---------------------+---------+

# add the rules. use zfs.profile for btrfs
$ wget https://raw.githubusercontent.com/ubuntu/microk8s/master/tests/lxc/microk8s-zfs.profile -O microk8s.profile

# pipe that `microk8s.profile` into the LXD profile.
$ cat microk8s.profile | lxc profile edit microk8s

# show profile
$ lxc profile show microk8s
config:
  boot.autostart: "true"
  linux.kernel_modules: ip_vs,ip_vs_rr,ip_vs_wrr,ip_vs_sh,ip_tables,ip6_tables,netlink_diag,nf_nat,overlay,br_netfilter
  raw.lxc: |
    lxc.apparmor.profile=unconfined
    lxc.mount.auto=proc:rw sys:rw cgroup:rw
    lxc.cgroup.devices.allow=a
    lxc.cap.drop=
  security.nesting: "true"
  security.privileged: "true"
description: ""
devices:
  aadisable:
....

# And then clean up downlaod profile
$ rm microk8s.profile
```
see [Explanation of the custom rules][4] for more details.

### Start an LXD container for MicroK8s
create the ubuntu 20.04 container with default &  microk8s profile

```shell
# create the container that MicroK8s will run in
$ lxc launch -p default -p microk8s ubuntu:20.04 microk8s

# Install MicroK8s in an LXD container
$ lxc exec microk8s -- sudo snap install microk8s --classic

```
`lxc launch -p default -p microk8s` command uses the `default` profile, for any existing system settings **(networking, storage, etc.)** before also applying the `microk8s` profile - the order is important.

### Install MicroK8s in an LXD container
```shell
$ lxc exec microk8s -- sudo snap install microk8s --classic
Download snap "microk8s" (2346) from channel "1.21/stable"
...

microk8s (1.21/stable) v1.21.3 from Canonical✓ installed
...
```


### Accessing MicroK8s Services Within LXD

```shell
$ lxc list microk8s
+----------+---------+----------------------+------+-----------+-----------+
|   NAME   |  STATE  |         IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+----------+---------+----------------------+------+-----------+-----------+
| microk8s | RUNNING | 10.14.193.113 (eth0) |      | CONTAINER | 0         |
+----------+---------+----------------------+------+-----------+-----------+

# Access Kubernetes
$ lxc exec microk8s -- su --login ubuntu

# show all
$ sudo microk8s.kubectl get all
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   12m

```

### Reset microk8s
Reset `microk8s` for refresh all configuration
```shell
$ sudo microk8s.reset
```

### Deploy an app
You’ll need to expose the **deployment or service** to the container. we will use [docker-microbot][6] as it provides a simple HTTP endpoint to expose.

```shell
# deployment
$ sudo microk8s.kubectl create deployment microbot --image=dontrebootme/microbot:v2
deployment.apps/microbot created

# check that the deployment has come up.
$ sudo microk8s.kubectl get all
NAME                 TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.152.183.1   <none>        443/TCP   32m

NAME                       READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/microbot   0/1     0            0           50s

# expose deployment
$ sudo microk8s.kubectl expose deployment microbot --type=NodePort --port=80 --name=microbot-service
service/microbot-service exposed

# get service
$ sudo microk8s.kubectl get service
NAME               TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP   10.152.183.1     <none>        443/TCP        39m
microbot-service   NodePort    10.152.183.164   <none>        80:31981/TCP   68s

$ sudo microk8s.kubectl get service microbot-service
NAME               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
microbot-service   NodePort   10.152.183.164   <none>        80:31981/TCP   77s

```

### MicroK8s use case: AI/ML and GPU acceleration
> Another significant use case is building AI/ML pipelines on top of MicroK8s. MicroK8s addresses both silos and infrastructure challenges, thanks to Kubeflow and GPU acceleration. Kubeflow bundles one of the latest and greatest collections of AI/ML tools that enable sharing of AI workflows between teams. Running Kubeflow on top of MicroK8s as easy as “microk8s.enable kubeflow”. GPU acceleration allows for model training compute requirements to be taken care of. Again, enabling GPU acceleration with MicroK8s is just a command away. \[[2]\]

[1]: https://microk8s.io "MicroK8s"

[2]: https://ubuntu.com/blog/what-can-you-do-with-microk8s "What is MicroK8s?"

[3]: https://en.wikipedia.org/wiki/Raft_(algorithm) "Raft Consensus Algorithm"

[4]: https://linuxcontainers.org/lxd/docs/master/storage#recommended-setup "Recommended setup"

[5]: https://microk8s.io/docs/lxd "MicroK8s in LXD"

[6]: https://github.com/dontrebootme/docker-microbot "docker-microbot"


https://sleeplessbeastie.eu/2020/10/07/how-to-install-kubernetes-on-lxd/
