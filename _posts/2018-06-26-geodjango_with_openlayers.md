---
layout: post
title:  "GeoDjango With OpenLayers4 and PostGIS"
categories: gis
published: true
---
## TemplateView
Renders a given template, with the context containing parameters captured in the URL.

### Core Settings TEMPLATES
[TEMPLATES](https://docs.djangoproject.com/en/2.0/ref/settings/#templates) is a
list containing the settings for all template engines to be used with Django.

By default setup, the Django template engine to load templates from
the templates subdirectory inside each installed application:
```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
...
    },
]
```

### Create a new one template
Create a new simple   **index.html** in the  `templates/network_topology` directory
under the network_topology application.
```
├── templates
│   └── network_topology
│       └── index.html
```

The following sample index.html file is the default HTML file
that appears in a browser when a user invokes **http://127.0.0.1:8000/network_topology/**
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <title>Network Topology with OpenLayers 4</title>
  <meta name="viewport" content="initial-scale=1.0, user-scalable=no, width=device-width">

</head>
<body>
   <p>Network Topology With OpenLayers 4 and PostGIS</p>
</body>
</html>
```

### [Writing IndexView](https://docs.djangoproject.com/en/2.0/topics/http/views/) in views.py
A view function, or view for short, is simply a Python function
that takes a Web request and returns a Web response.
```
from django.shortcuts import render

# Create your views here.

from django.views.generic.base import View, TemplateView
from django.http.response import HttpResponse

class IndexView(TemplateView):
    # a template file name in the templates directory in current application
    template_name = 'network_topology/index.html'

    def get_context_data(self, **kwargs):
        return kwargs
```


### Edit URLconf Application
Then we just need to add this new view into our URLconf
```
from django.urls import path
from .views import IndexView

app_name = 'network_topology'
urlpatterns = [
    path('', IndexView.as_view(), name='index'),
]
```

### Edit URLconf Project
Append the `urlpatterns` variable

```
urlpatterns = [
    path('admin/', admin.site.urls),
...
    path('network_topology/', include('network_topology.urls', namespace="network_topology")),
]
```
