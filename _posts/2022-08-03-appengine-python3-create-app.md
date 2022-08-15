---
layout : post
title : "Building a Python 3 Dash App on App Engine standard environment"
categories : [gcp]
published : true
---

This is summary from : [Building a Python 3 Dash App on App Engine standard environment](https://cloud.google.com/appengine/docs/standard/python3/building-app)


### [Setting up a Python development environment](https://cloud.google.com/python/docs/setup#linux)

```shell
# Install the latest version of Python
sudo apt update
sudo apt install python3 python3-dev python3-venv

# Install pip to get the latest version
sudo apt-get install wget
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py

# Verify that you have pip installed
pip3 --version
```

### Use virtualenv to isolate dependencies

```shell
cd your-project

# initializes an empty git repo
git init  

# creates a virtualenv called "env"
python3 -m venv env

# uses the virtualenv
source env/bin/activate 
```

### Install app's dependencies with this virtualenv

```shell
pip install dash
pip install plotly
```

### Create `.gcloudignore`

```
# This file specifies files that are *not* uploaded to Google Cloud Platform
# using gcloud. It follows the same syntax as .gitignore, with the addition of
# "#!include" directives (which insert the entries of the given .gitignore-style
# file at that point).
#
# For more information, run:
#   $ gcloud topic gcloudignore
#
.gcloudignore
# If you would like to upload your .git directory, .gitignore file or files
# from your .gitignore file, remove the corresponding line
# below:
.git
.gitignore

# Python pycache:
__pycache__/
# Ignored by the build system
/setup.cfg

# images
/images/*

# key
data/key/*

# virtualenv
env
```

### [Enable the Cloud Build API](https://cloud.google.com/appengine/docs/standard/python3/building-app/creating-gcp-project)

If not enable the Cloud Build API will get this error:
```
ERROR: (gcloud.app.deploy) Error Response: [7] Access Not Configured. Cloud Build has not been used in project kfinviz before or it is disabled. Enable it by visiting https://console.developers.google.com/apis/api/cloudbuild.googleapis.com/overview?project=kfinviz then retry. If you enabled this API recently, wait a few minutes for the action to propagate to our systems and retry.
```


**app.yaml:**
```
runtime: python310

resources:
    cpu: 1
    memory_gb: 1
    disk_size_gb: 1
```
if not set `disk_size_gb` will get this error:
```
ERROR: (gcloud.app.deploy) Error Response: [13] Container 'asia.gcr......' status is ERROR; Failed to move user code into storage, please verify the pod configuration and try it again.
````

**requirements.txt:**
```
dash==2.6.1
Flask==2.2.0
plotly==5.9.0
pandas==1.3.5
```

### Deploy and run Dash App on App Engine


```shell
gcloud app deploy

Services to deploy:

descriptor:                  [/home/kitcharoenp/respo/kfin/app.yaml]
source:                      [/home/kitcharoenp/respo/kfin]
target project:              [kfin-358305]
target service:              [default]
target version:              [20220803t151304]
target url:                  [https://kfin-358305.et.r.appspot.com]
target service account:      [App Engine default service account]


Do you want to continue (Y/n)? 

Beginning deployment of service [default]...
╔════════════════════════════════════════════════════════════╗
╠═ Uploading 3 files to Google Cloud Storage                ═╣
╚════════════════════════════════════════════════════════════╝
File upload done.
Setting traffic split for service [default]...done.                                                                                          
Deployed service [default] to [https://kfinviz.de.r.appspot.com]

You can stream logs from the command line by running:
  $ gcloud app logs tail -s default

To view your application in the web browser run:
  $ gcloud app browse


```

### Launch your browser to view the app

```shell
gcloud app browse
```



