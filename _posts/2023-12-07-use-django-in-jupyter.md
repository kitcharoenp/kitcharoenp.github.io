---
layout : post
title : "Use Django in Jupyter"
categories : [django]
published : true
---




### Install Jupyter Notebooks in a Virtual Environment


```shell
# Activate your virtualenv
$ source vir_env/bin/activate

#  Install Jupyter
(vir_env) $ pip install jupyter notebook
```


### Check Jupyter version

```shell
# Check version
(vir_env) $ jupyter --version
Selected Jupyter core packages...
IPython          : 7.16.3
ipykernel        : 5.5.6
ipywidgets       : 7.8.1
jupyter_client   : 7.1.2
jupyter_core     : 4.9.2
jupyter_server   : not installed
jupyterlab       : not installed
nbclient         : 0.5.9
nbconvert        : 6.0.7
nbformat         : 5.1.3
notebook         : 6.4.10
qtconsole        : 5.2.2
traitlets        : 4.3.3

# Start Jupyter
(vir_env) $ jupyter notebook
[I 14:39:47.776 NotebookApp] Serving notebooks from local directory: /home/ubuntu/kapany_proj
[I 14:39:47.776 NotebookApp] Jupyter Notebook 6.4.10 is running at:
[I 14:39:47.776 NotebookApp] http://localhost:8888/
[I 14:39:47.776 NotebookApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
[W 14:39:47.779 NotebookApp] No web browser found: could not locate runnable browser

```

### Add Virtual Environment to Jupyter Notebook

```shell
# Install ipykernel which provides the IPython kernel for Jupyter
(vir_env) $ pip install ipykernel

# Add your virtual environment to Jupyter
(vir_env) $ python -m ipykernel install --user --name=vir_env
Installed kernelspec vir_env in /home/ubuntu/.local/share/jupyter/kernels/vir_env

# This should print the following:
Installed kernelspec vir_env in /home/user/.local/share/jupyter/kernels/vir_env

```

### Access Jupyter Notebook Remotely
```shell
# Protect notebook with password 
(vir_env) $ jupyter notebook password

#  Run the jupyter notebook on server 
(vir_env) $ jupyter notebook --no-browser --ip='your server IP Address' --port=8888
```



### Use Django in Jupyter

**Initialize Django in the first cell:**
```python
import os, sys
import django
PROJECTPATH = '/my/django/project/path'
sys.path.insert(0, PROJECTPATH)
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "my.django.settings.module") # myproject.settings
os.environ["DJANGO_ALLOW_ASYNC_UNSAFE"] = "true"  # https://docs.djangoproject.com/en/4.1/topics/async/#async-safety
os.chdir(PROJECTPATH)
django.setup()
```


### Get Django Object to dataframe
```python
from pwauser.models import PwaUser
PwaUser.objects.count()

import pandas as pd
df = pd.DataFrame(list(PwaUser.objects.all().values()))
df[~df['last_login'].isna()]
```

### Reference 

* [Install Jupyter Notebooks in a Virtual Environment](https://www.codingforentrepreneurs.com/blog/install-jupyter-notebooks-virtualenv/)

* [Add Virtual Environment to Jupyter Notebook](https://janakiev.com/blog/jupyter-virtual-envs/#add-virtual-environment-to-jupyter-notebook)

* [How to Access Jupyter Notebook Remotely on Webbrowser](https://dev.to/iamtekson/how-to-access-jupyter-notebook-remotely-on-webbrowser-3jje)

* [Using Django project in Jupyter or JupyterLab](https://gist.github.com/EtsuNDmA/dd8949061783bf593706559374c8f635)

* [Use Django in Jupyter](https://www.codingforentrepreneurs.com/blog/use-django-in-jupyter/)

