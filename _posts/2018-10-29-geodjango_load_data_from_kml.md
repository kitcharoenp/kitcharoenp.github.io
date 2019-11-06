---
layout: post
title: "GeoDjango Import Data from KML File"
published: true
categories: [gis]
---
There are some cases, you need to import the KML data to a Django project.
Share and collaborate on KML has a complex folder structure (many sub-folders
in sub-folder). If a KML is the flat-folder structure, you could use
[LayerMapping](https://docs.djangoproject.com/en/2.1/ref/contrib/gis/layermapping/) to extract all the features and place them in the database.
This is my foolish code to accomplish it.

```
# My Project Structure
.
├── my_env
├── my_proj
├── manage.py
├── my_app
└── static

# my_app Structure
.
├── admin.py
├── apps.py
├── data
├── __init__.py
├── load_lines.py
├── migrations
├── models
├── __pycache__
├── tests.py
├── urls.py
└── views

# my_line Model
class Line(models.Model):
    """
    Model representing a geometry line string
    """
    name = models.CharField(max_length=254)
    geom = models.LineStringField(srid=4326, null=True, blank=True,)
    layer_name = models.CharField(max_length=254, blank=True, null=True,)
    description = models.TextField(max_length=1000, blank=True,null=True,)
```

#### Place your KML file into the `data` app folder:

```
# my_app/data
.
├── metro.kml
```

#### Create a `load_lines.py` in `my_app` for extract the LineString geometry from KML.
Copy and paste code in each section into your `load_lines.py`

#### Import Modules

```
import os
from django.contrib.gis.gdal import DataSource
from django.contrib.gis.geos import GEOSGeometry, LineString, WKTWriter
from my_app.models import Line
```

#### Reading Data

```
kml_file = os.path.abspath(
    os.path.join(os.path.dirname(__file__), 'data', 'metro.kml'),
)
# Reading Data by DataSource
source = DataSource(kml_file)
```

#### Make a `get_feat_property()` Function

```
def get_feat_property(feat):
       # Get the feature property.
    property = {
        'name': feat.get('Name'),
        'description': feat.get('description'),
    }
    return property
```

#### Make a `run()` function

```
# Writes the Well-Known Text representation of a Geometry.
wkt_w = WKTWriter()

def run(verbose=True):
    # Iterating Over Layers
    for layer in source:
        for feat in layer:
            if(feat.geom.geom_type.name.startswith('Line')):
                # Get the feature geometry.
                geom = feat.geom

                # get the feature property
                property = get_feat_property(feat)

                if (len(geom.coords) >= 2 ):
                    line = Line.objects.create(
                        name = property['name'],
                        description = property['description'],
                        layer_name = feat.layer_name,
                        # Make a GeosGeometry object
                        geom = GEOSGeometry(wkt_w.write(geom.geos)),                    
                    )
```

#### Import the Spatial Data into GeoDjango models

```
$ python manage.py shell

>>> from my_app import load_lines
>>> load_lines.run()
```
