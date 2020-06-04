---
layout: post
title: "Ubuntu iptable"
published: true
categories: [server]
---
> Iptables is a firewall, installed by default on all official Ubuntu distributions (Ubuntu, Kubuntu, Xubuntu). When you install Ubuntu, iptables is there, but it allows all traffic by default. [1][[1]]

### Show `NAT`
```shell
$ sudo iptables -t nat -v -L -n --line-number
...
Chain PREROUTING (policy ACCEPT 1465K packets, 97M bytes)
num   pkts bytes target     prot opt in     out     source               destination         
1      192  9920 DNAT       tcp  --  *      *       0.0.0.0/0            192.168.1.1         tcp dpt:8077 to:10.10.10.10:8000
2     947K   49M DNAT       tcp  --  *      *       0.0.0.0/0            192.168.1.1         tcp dpt:8078 to:10.10.10.11:80
3     2168  128K DNAT       tcp  --  *      *       0.0.0.0/0            192.168.1.1         tcp dpt:8075 to:10.10.10.12:8000
```

### Delete `PREROUTING NAT`

```shell
sudo iptables -t nat -D PREROUTING 3 # 3 is Chain num
```

### Create `PREROUTING NAT`
NAT `192.168.1.1:8071` to container `10.10.10.19:8069`
```shell
sudo iptables -t nat -A PREROUTING -d 192.168.1.1 -p tcp --dport 8071 -j DNAT --to-destination 10.10.10.19:8069
```

### Save iptable
Executing the following commands as **root**
```shell
iptables-save > /etc/iptables/rules.v4
```

[1]: https://help.ubuntu.com/community/IptablesHowTo "IptablesHowTo"
