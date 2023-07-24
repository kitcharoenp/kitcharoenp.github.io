---
layout : post
title : "IPMItool cofig HP iLO 4 through Linux"
categories : [ubuntu, hp, ilo]
published : true
---

### [Installing IPMItool on Ubuntu with apt](https://phoenixnap.com/kb/install-ipmitool-ubuntu-centos)


```shell
$ sudo apt install ipmitool
```

### Show LAN interface
HP iLO 4, LAN interface assigned to channel number `2`
```shell
$ sudo ipmitool channel info 2
Channel 0x2 info:
  Channel Medium Type   : 802.3 LAN
  Channel Protocol Type : IPMB-1.0
  Session Support       : multi-session
  Active Session Count  : 0
  Protocol Vendor ID    : 7154
  Volatile(active) Settings
    Alerting            : enabled
    Per-message Auth    : disabled
    User Level Auth     : enabled
    Access Mode         : always available
  Non-Volatile Settings
    Alerting            : enabled
    Per-message Auth    : disabled
    User Level Auth     : enabled
    Access Mode         : always available
```

### LAN Configuration
**2** is LAN interface channel for HP iLO 4
```shell
# set IP Address Source : Static
$ sudo ipmitool lan set 2 ipsrc static

# IP Address
$ sudo ipmitool lan set 2 ipaddr 10.10.10.110

# Subnet Mask
$ sudo ipmitool lan set 2 netmask 255.255.255.0

# Default Gateway IP
$ sudo ipmitool lan set 2 defgw ipaddr 10.10.10.20
```

### Show network settings
Get network settings configured on the HP iLO port

```shell
$ sudo ipmitool lan print 2
Set in Progress         : Set Complete
Auth Type Support       : 
Auth Type Enable        : Callback : 
                        : User     : 
                        : Operator : 
                        : Admin    : 
                        : OEM      : 
IP Address Source       : Static Address
IP Address              : 10.10.10.110
Subnet Mask             : 255.255.255.0
MAC Address             : 14:02:ec:62:32:3d
SNMP Community String   : 
BMC ARP Control         : ARP Responses Enabled, Gratuitous ARP Disabled
Default Gateway IP      : 10.10.10.20
802.1q VLAN ID          : Disabled
802.1q VLAN Priority    : 0
RMCP+ Cipher Suites     : 0,1,2,3
Cipher Suite Priv Max   : XuuaXXXXXXXXXXX
                        :     X=Cipher Suite Unused
                        :     c=CALLBACK
                        :     u=USER
                        :     o=OPERATOR
                        :     a=ADMIN
                        :     O=OEM
Bad Password Threshold  : Not Available
```

## Configuring users

List users in the `channel number 2`
```shell
$ sudo ipmitool user list 2
ID  Name	     Callin  Link Auth	IPMI Msg   Channel Priv Limit
1   Administrator    true    false      true       ADMINISTRATOR
2   (Empty User)     true    false      false      NO ACCESS
3   (Empty User)     true    false      false      NO ACCESS
4   (Empty User)     true    false      false      NO ACCESS
5   (Empty User)     true    false      false      NO ACCESS
6   (Empty User)     true    false      false      NO ACCESS
7   (Empty User)     true    false      false      NO ACCESS
8   (Empty User)     true    false      false      NO ACCESS
9   (Empty User)     true    false      false      NO ACCESS
10  (Empty User)     true    false      false      NO ACCESS
11  (Empty User)     true    false      false      NO ACCESS
12  (Empty User)     true    false      false      NO ACCESS

```

### Change password

`Administrator` user have ID 1
```shell
# Administrator ID is `1`
$ sudo ipmitool user set password 1
Password for user 1: 
Password for user 1: 
Set User Password command successful (user 1)
$
```

### Create a user
Create a user with admin rights.
```shell
# Set user ID 2 with username `Adm1nistrator`
$ sudo ipmitool user set name 2 Adm1nistrator

# Set password  user ID 2
$ sudo ipmitool user set password 2
Password for user 2:
Password for user 2:
# set channel 2 (Lan Interface) access to user ID 2 with privilege=4
$ sudo ipmitool channel setaccess 2 2 link=on ipmi=on callin=on privilege=4

# Enable user ID 2
$ sudo ipmitool user enable 2
```

### Restart iLO interface
For a **cold reset** (forcefully, in case iLO is not responding in any way including echo requests/ping) use the following:

```shell
$ sudo ipmitool mc reset cold
```

### iLO web html5 interface
Access iLO via `https://ilo_ip_address` such as https://10.10.10.110

![hp_ilo_4](/assets/img/blog/hp_ilo_4.png)

if `10.10.10.110` is local ip address and you can access it.

### Reference
* [IPMItool considerations](https://www.ibm.com/docs/en/power8/8348-21C?topic=overview-ipmitool-considerations)

* [Configuring HP iLO through Linux](https://dev-random.net/configuring-hp-ilo-through-linux-automatically/)

* [Configuring IPMI under Linux using ipmitool](https://www.thomas-krenn.com/en/wiki/Configuring_IPMI_under_Linux_using_ipmitool)

* [How to Install IPMItool on Centos 7/8 & Ubuntu 18.04/20.04](https://phoenixnap.com/kb/install-ipmitool-ubuntu-centos)