---
layout: post
title: "Apache2: Harden"
categories: [server]
---

### Settings sysctl
**`/etc/sysctl.conf`:**
```
# IP Spoofing protection
net.ipv4.conf.all.rp_filter = 1
net.ipv4.conf.default.rp_filter = 1

# Ignore ICMP broadcast requests
net.ipv4.icmp_echo_ignore_broadcasts = 1

# Disable source packet routing
net.ipv4.conf.all.accept_source_route = 0
net.ipv6.conf.all.accept_source_route = 0
net.ipv4.conf.default.accept_source_route = 0
net.ipv6.conf.default.accept_source_route = 0

# Ignore send redirects
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0

# Block SYN attacks
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.tcp_synack_retries = 2
net.ipv4.tcp_syn_retries = 5

# Log Martians
net.ipv4.conf.all.log_martians = 1
net.ipv4.icmp_ignore_bogus_error_responses = 1

# Ignore ICMP redirects
net.ipv4.conf.all.accept_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0

# Ignore Directed pings
net.ipv4.icmp_echo_ignore_all = 1
```
**reload sysctl**
```shell
sudo sysctl -p
```

### Using Mod_security
> ModSecurity is an open source, cross-platform web application firewall (WAF) module.
Known as the "Swiss Army Knife" of WAFs, it enables web application defenders
to gain visibility into HTTP(S) traffic and provides a power rules language and API to implement advanced protections. [[2]]

```shell
$ sudo apt install libapache2-mod-security2 -y

$ sudo systemctl restart apache2
```
check if the module is enabled
```shell
$ sudo apachectl -M | grep security
```
should get the below output:
```
security2_module (shared)
```


### Using Mod_evasive
Detects and provides protection against DDOS and HTTP brute force attacks.

```shell
$ sudo apt install libapache2-mod-evasive -y
$ sudo systemctl restart apache2
```


[1]: https://www.dexbot.info/2018/06/07/step-by-step-guide-to-secure-an-ubuntu-16-04-lts-server-part-1-of-2/ "secure an Ubuntu"

[2]: https://modsecurity.org/download.html "ModSecurity"

[3]: https://www.liquidweb.com/kb/install-and-configure-mod_security-on-ubuntu-16-04-server/ "Install and Configure ModSecurity"

[4]: https://hostadvice.com/how-to/how-to-setup-modsecurity-for-apache-on-ubuntu-18-04/ "title"
