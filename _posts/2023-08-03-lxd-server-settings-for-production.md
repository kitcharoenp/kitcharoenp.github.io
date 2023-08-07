---
layout : post
title : "Server settings for a LXD production setup"
categories : [ubuntu, lxd]
published : true
---

see a list of all your user’s limits on the system 
```
$ ulimit -a
```


### `/etc/security/limits.conf`
add the following content:

```
#Each line describes a limit for a user in the form:
#
#<domain>        <type>  <item>  <value>
#
# Server settings for a LXD production setup
#
# Maximum number of open files
*	soft	nofile	1048576	

# Maximum number of open files
*	hard	nofile	1048576	

# Maximum number of open files
root	soft	nofile	1048576	

# Maximum number of open files
root	hard	nofile	1048576	

# Maximum locked-in-memory address space (KB)
*	soft	memlock	unlimited	

# Maximum locked-in-memory address space (KB)
*	hard	memlock	unlimited	

# Maximum locked-in-memory address space (KB), only need with bpf syscall supervision
root	soft	memlock	unlimited	

# Maximum locked-in-memory address space (KB), only need with bpf syscall supervision
root	hard	memlock	unlimited	

```

* `*` denotes the rest of the system users
* `soft` or `hard` is the limit type
* `nofile` item is utilized for limiting


### `/etc/sysctl.conf`
add the following content:

```
#
# Server settings for a LXD production setup
#


# Maximum number of concurrent asynchronous I/O operations 
# (you might need to increase this limit further if you have 
# a lot of workloads that use the AIO subsystem, for example, MySQL)
# fs.aio-max-nr = 524288	# default recommend
fs.aio-max-nr = 1048576

# Upper limit on the number of events that can be queued to 
# the corresponding inotify instance (see inotify)
fs.inotify.max_queued_events = 1048576	

# Upper limit on the number of inotify instances that 
# can be created per real user ID (see inotify)
fs.inotify.max_user_instances = 1048576	

# Upper limit on the number of watches that can be 
# created per real user ID (see inotify)
fs.inotify.max_user_watches = 1048576	

# Whether to deny container access to the messages in the kernel 
# ring buffer (note that this will also deny access to non-root users on the host system)
kernel.dmesg_restrict = 1	

# Maximum size of the key ring that non-root users can use
kernel.keys.maxbytes = 2000000	

# Maximum number of keys that a non-root user can use 
# (the value should be higher than the number of instances)
kernel.keys.maxkeys = 2000	

# Limit on the size of eBPF JIT allocations (on kernels < 5.15 
# that are compiled with CONFIG_BPF_JIT_ALWAYS_ON=y, 
# this value might limit the amount of instances that can be created)
net.core.bpf_jit_limit = 1000000000	

# Maximum number of entries in the IPv4 ARP table 
# (increase this value if you plan to create over 1024 instances - 
# otherwise, you will get the error neighbour: ndisc_cache: neighbor 
# table overflow! when the ARP table gets full and the instances 
# cannot get a network configuration; see ip-sysctl)
net.ipv4.neigh.default.gc_thresh3 = 8192	

# Maximum number of entries in IPv6 ARP table 
# (increase this value if you plan to create over 1024 instances - 
# otherwise, you will get the error neighbour: ndisc_cache: 
# neighbor table overflow! when the ARP table gets full 
# and the instances cannot get a network configuration; see ip-sysctl)
net.ipv6.neigh.default.gc_thresh3 = 8192	

# Maximum number of memory map areas a process may have 
# (memory map areas are used as a side-effect of calling malloc, 
# directly by mmap and mprotect, and also when loading shared libraries)
vm.max_map_count = 262144	
```


To activate the new setting, run the following command:
```shell
$ sudo sysctl -p /etc/sysctl.conf
```

### Reference
* [Server settings for a LXD production setup](https://documentation.ubuntu.com/lxd/en/latest/reference/server_settings/#server-settings)