---
layout : post
title : "Two Factor Authentication in Django Admin Panel"
categories : [django]
published : true
---

### Check Django version

```python
>>> import django
>>> django.VERSION
(2, 0, 7, 'final', 0)
>>> 

```

### install the packages
```shell
pip install -U django-otp==0.5.0 qrcode Django==2.0.7


pip install -U django-user-accounts==2.1.0  Django==2.0.7
```

### Reference
* [Two Factor Authentication in Django Admin Panel](https://auberginesolutions.com/blog/quick-start-two-factor-authentication-in-django-admin-panel/)