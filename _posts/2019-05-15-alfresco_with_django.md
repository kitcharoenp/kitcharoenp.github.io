---
layout: post
title: "Using Alfresco with Django in Celery Task"
published: false
categories: [django, alfresco, file]
---

### Media Files Configurations
Add the following to `proj/proj/settings.py` . See more in [Managing files](https://docs.djangoproject.com/en/2.2/topics/files/#managing-files)
```python
# Absolute filesystem path to the directory that will hold user-uploaded files.
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

#URL that handles the media served from MEDIA_ROOT, used for managing stored files.
MEDIA_URL = '/media/'
```

### Settings Alfresco Connection parameters
Add the following to `proj/proj/local_settings.py`
```python
# Alfresco Connection Parameters
ALFRESCO_BASE_URL = 'http://your_alfreco_host/alfresco/service/api/upload'
ALFRESCO_AUTH_USERNAME = 'your_alf_username'
ALFRESCO_AUTH_PASSWORD = 'your_alf_password'
ALFRESCO_SITE_ID = 'your_alf_site_id'
```

### Example Uploading Module
*  Create a new `alfresco_files_upload.py` in `proj/app/views/`
```shell
$ touch proj/app/views/alfresco_files_upload.py
$ tree proj/app/views/ -L 1
proj/app/views/
├── alfresco_files_upload.py
├── __init__.py
```

* Add the following lines to `proj/app/views/__init__.py`
```python
from .alfresco_files_upload import *
```

* **file:** `alfresco_files_upload.py`
```python
from django.conf import settings
from django.core.files.storage import FileSystemStorage

import json
import requests
import os

def alfresco_set_conn_param():
    auth_username = settings.ALFRESCO_AUTH_USERNAME
    auth_password = settings.ALFRESCO_AUTH_PASSWORD
    url = settings.ALFRESCO_BASE_URL
    siteid = settings.ALFRESCO_SITE_ID

    auth = (auth_username, auth_password)
    data = {"siteid": siteid, "containerid": "documentLibrary"}
    return url, auth, data

def alfresco_upload_a_file(file_system, file_name):
    # set the alfresco connection param
    url, auth, data = alfresco_set_conn_param()

    files = {"filedata": file_system.open(file_name, mode='r')}

    r = requests.post(url, auth=auth, data=data, files=files)
    return r

def alfresco_upload_dir(directory='kml/', json_result = ''):
    path = os.path.join(settings.MEDIA_ROOT, directory)
    file_system = FileSystemStorage(location=path)

    for f in file_system.listdir(path)[1]:
        r = alfresco_upload_a_file(file_system, f)
        data_ = r.json()
        json_result += json.dumps(data_)
    return json_result        
```

### Define Task
My uploading files in `proj/media/upload_to_alf/`

**file:** proj/app/tasks.py
```python
from __future__ import absolute_import, unicode_literals
from proj.celery import app
from . import views

@app.task
def alfresco_upload_dir(directory='upload_to_alf/'):
    return views.alfresco_upload_dir(directory=directory)
```

### Testing on Periodic Tasks Administration

### View Celery Results › Task results
