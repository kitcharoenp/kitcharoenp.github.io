---
layout: post
title: "Geodjango export geometries to kml file"
published: false
categories: [geodjango]
---

### Media Files Configurations
Add the following to `proj/proj/settings.py` . See more in [Managing files](https://docs.djangoproject.com/en/2.2/topics/files/#managing-files)
```python
# Absolute filesystem path to the directory that will hold user-uploaded files.
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

#URL that handles the media served from MEDIA_ROOT, used for managing stored files.
MEDIA_URL = '/media/'
```
