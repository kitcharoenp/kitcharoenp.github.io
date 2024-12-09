---
layout : post
title : "PHP.ini configuration for zurmo"
categories : [zurmo]
published : false
---

### Display `php.ini` config
```shell
$ grep '^[[:blank:]]*[^[:blank:]#;]' /etc/php/7.2/fpm/php.ini
```


### php.ini
```
; How many GET/POST/COOKIE input variables may be accepted
; max_input_vars = 1000
;
; Fixed : member of groups is empty when edit group member
max_input_vars = 3000

; Fixed : Fatal error: Allowed memory size of 134217728 bytes exhausted (tried to allocate 20480 bytes)
memory_limit = -1
```

### Class
* comment `ini_set()` in **CHttpSession** [yii/framework]


### Database
* `globalmetadata` rename `MaterialsControl` classname

### ERROR
```log
Sep 24 12:34:35 xserver02 systemd-networkd-wait-online[494]: Event loop failed: Connection timed out
Sep 24 12:34:35 xserver02 systemd[1]: systemd-networkd-wait-online.service: Main process exited, code=exited, status=1/FAILURE
Sep 24 12:34:35 xserver02 systemd[1]: systemd-networkd-wait-online.service: Failed with result 'exit-code'.
Sep 24 12:34:35 xserver02 systemd[1]: Failed to start Wait for Network to be Configured.
```



```
audit: type=1400 audit(1632986733.064:374): apparmor="DENIED"
```


DROP PROCEDURE IF EXISTS
```
2021/09/29 09:06:10 [error] [exception.RedBean_Exception_SQL] [42000] - SQLSTATE[42000]: Syntax error or access violation: 1304 PROCEDURE get_group_inherited_actual_right_ignoring_everyone already exists
```


```
Syntax error or access violation: 1304 PROCEDURE get_group_inherited_actual_right_ignoring_everyone already exists

SQLSTATE[42000]: Syntax error or access violation: 1305 FUNCTION zurmo.get_securableitem_actual_permissions_for_permitable does not exist
```

```
SQLSTATE[42000]: Syntax error or access violation: 1304 FUNCTION get_permitable_explicit_actual_right already exists
```

SQLSTATE[42000]: Syntax error or access violation: 1304 PROCEDURE recursive_group_contains_user already exists


SQLSTATE[42000]: Syntax error or access violation: 1304 FUNCTION get_user_actual_right already exists
