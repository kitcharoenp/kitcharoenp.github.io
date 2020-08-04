---
layout: post
title: "Apache2: Harden ModSecurity2"
categories: [server]
---

## Using Mod_security [[3]]
> ModSecurity is an open source, cross-platform web application firewall (WAF) module.
Known as the "Swiss Army Knife" of WAFs, it enables web application defenders
to gain visibility into HTTP(S) traffic and provides a power rules language and API to implement advanced protections. [[2]]

### Installing
```shell
$ sudo apt install libapache2-mod-security2 -y

# List dependent packages
$ apt-cache rdepends libapache2-mod-security2
libapache2-mod-security2
Reverse Depends:
  modsecurity-crs
  libapache2-modsecurity

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

### Edit the configuration file
> ModSecurity engine needs rules to work. ModSecurity can pass, drop, redirect,
execute a script or even display a status code during a session. [[3]]


```shell
# location of Mod_security : /etc/modsecurity/
$ cd /etc/modsecurity/
$ sudo cp modsecurity.conf-recommended modsecurity.conf
$ sudo nano modsecurity.conf
```

change `SecRuleEngine DetectionOnly` to `SecRuleEngine On`
```
#SecRuleEngine DetectionOnly
SecRuleEngine On
```

save `modsecurity.conf` then check config / restart apache2
```
$ sudo apache2ctl -t && sudo apache2ctl restart
```
> This will turn on ModSecurity using the basic default rules. [[3]]

### Enable Core Rule Set
> The `libapache2-modsecurity` package comes with a companion package (`modsecurity-crs`). This package contains the Core Rule Set or CRS, which is a basic set of rules that handle some of the most common malicious activity on the Internet.  [[3]]

### Using Mod_evasive
Detects and provides protection against DDOS and HTTP brute force attacks.

```shell
$ sudo apt install libapache2-mod-evasive -y
$ sudo systemctl restart apache2
```



[1]: https://www.dexbot.info/2018/06/07/step-by-step-guide-to-secure-an-ubuntu-16-04-lts-server-part-1-of-2/ "Secure an Ubuntu"

[2]: https://www.liquidweb.com/kb/install-and-configure-mod_security-on-ubuntu-16-04-server/ "Install and Configure ModSecurity"

[3]: https://phoenixnap.com/kb/setup-configure-modsecurity-on-apache "Configure ModSecurity On Apache"

[4]: https://owasp.org/www-project-modsecurity-core-rule-set/ "OWASP ModSecurity Core Rule Set"
