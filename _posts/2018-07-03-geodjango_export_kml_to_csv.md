---
layout: post
title:  "GeoDjango Export KML to CSV"
categories: gis
published: true
---

We will create a python scripts to parse a KML to CSV file. We use the external shared libraries GEOS and GDAL in GeoDjango to perform.

[**GEOS**](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/geos/) is a C++ library for performing geometric operations,
and is the default internal geometry representation used by GeoDjango.

[**GDAL**](GDAL stands for Geospatial Data Abstraction Library, and is a veritable “Swiss army knife” of GIS data functionality.)
stands for **Geospatial Data Abstraction Library**, and is a veritable “Swiss army knife” of GIS data functionality.

### Import Libraries
```
from django.contrib.gis.gdal import DataSource
from django.contrib.gis.geos import GEOSGeometry
import csv
```
* [DataSource](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/gdal/#django.contrib.gis.gdal.DataSource)
 is a wrapper for the OGR data source object that supports reading
data from a variety of OGR-supported geospatial file formats and data sources
using a simple, consistent interface.
* [GEOSGeometry](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/geos/#django.contrib.gis.geos.GEOSGeometry)
is the base class for all GEOS geometry objects.
* [csv](https://docs.python.org/2/library/csv.html) implements classes to read and write tabular data in CSV format.

### Append data to CSV File
Make a function which append the new row to CSV file.
```
def writerow_append_csv(csvfile, row):
    with open(csvfile, "a") as output:
        writer = csv.writer(output, lineterminator='\n')
        writer.writerow(row)
```

### Extracting Layer Features from GDAL DataSource
Make a function extracting the layer features **(layer_name, geom_type, Name, description)**
from GDAL DataSource. We only get GEOM type name starts with `Point` and `LineString`.
Then use `writerow_append_csv` function to write
features to CSV file.
```
def write_layer_features_to_csv(layer):
    for feat in layer:
        if(feat.geom.geom_type.name.startswith('Point')):
            r_point = []
            r_point += [feat.layer_name,
                        feat.geom.geom_type.name,
                        feat.geom.coords,
                        feat.get('Name'),
                        feat.get('description')]
            # write point to csv
            writerow_append_csv(pointsFile, r_point)
        elif(feat.geom.geom_type.name.startswith('LineString')):
            line_string = []                                     
            line_string += [feat.layer_name,
                            feat.geom.geom_type.name,
                            feat.geom.coords,
                            feat.get('Name'),
                            feat.get('description')]
            # write line string to csv
            writerow_append_csv(lineStringsFile, line_string)
        else:
            print(feat.geom.geom_type.name, feat.get('Name'))    
```

### Iteration via DataSource Layer
```
pointsFile = "points.csv"
lineStringsFile = "lineStrings.csv"

# define the column header of  csv 
header =  ['layer_name', 'geom_type', 'coords', 'name', 'description']
for csvfile in [pointsFile, lineStringsFile]:
    with open(csvfile, "w") as output:
        writer = csv.writer(output, lineterminator='\n')
        writer.writerow(header)
source = DataSource('MEA.kml')

# iteration via layer in source if found Point or lineStrings writer it to csv file
for layer in source:
    write_layer_features_to_csv(layer)        
```
