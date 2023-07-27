---
layout : post
title : "LXD : Failed to change ACLs"
categories : [ubuntu, lxd]
published : false
---
### Cannot start a copied container: Failed to change ACLs


$ lxc start limeSurvey
Error: Failed to handle idmapped storage: invalid argument - Failed to change ACLs on /var/snap/lxd/common/lxd/storage-pools/local/containers/limeSurvey/rootfs/var/log/journal
Try `lxc info --show-log limeSurvey` for more info


Deleting the directory /var/log/journal of the container indeed did the trick! I 

```
```

lxc copy server01:limeSurvey/snap-3980 limeSurvey-00 --target server03 --storage local

### Reference
* [Cannot start a copied container: Failed to change ACLs](https://discuss.linuxcontainers.org/t/cannot-start-a-copied-container-failed-to-change-acls/7982)

* https://github.com/canonical/lxd/issues/6771
