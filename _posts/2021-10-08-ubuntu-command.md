---
layout : post
title : "Ubuntu : Command"
categories : [ubuntu]
published : false
---

### Find files and directory then delete

delete file / directory last modified more than 1200 days
``` shell
find -mtime +1200 -exec rm -rv {} +
```