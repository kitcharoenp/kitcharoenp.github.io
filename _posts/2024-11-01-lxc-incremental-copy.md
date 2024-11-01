---
layout : post
title : "LXC Copy : Perform an incremental copy"
categories : [lxc]
published : true
---

```shell
lxc copy remote_name:source_name destination_name --refresh --mode  push
```

* `--refresh`              Perform an incremental copy

Transfer modes (`--mode`):

* `pull`: Target server pulls the data from the source server (source must listen on network)
* `push`: Source server pushes the data to the target server (target must listen on network)
* `relay`: The CLI connects to both source and server and proxies the data (both source and target must listen on network)
