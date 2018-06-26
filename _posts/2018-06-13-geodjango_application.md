---
layout: post
title:  "Geodjango Loading a Shapefile into PostGIS"
categories: gis
published: true
---
This post do following [GeoDjango Tutorial](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/tutorial/). In this
post we will loading a shapefile then convert it for import to PostGIS. We will use
boundaries to filter point and line which display on map.

## Geographic Data
### Thailand - Administrative Boundaries
The [Thai district boundaries (administrative level 2)](https://data.humdata.org/dataset/thailand-administrative-boundaries) data is available in this [zip file](https://data.humdata.org/dataset/d24bdc45-eb4c-4e3d-8b16-44db02667c27/resource/baecf366-00d9-425e-9ff5-e583d187374c/download/tha_adm2_gista_plyg_v5.zip).
* Create a **data** directory in the **network_topology** application
* Download the Thai district boundaries data
* unzip

```
$ mkdir network_topology/data
$ cd network_topology/data
$ wget https://data.humdata.org/dataset/d24bdc45-eb4c-4e3d-8b16-44db02667c27/resource/baecf366-00d9-425e-9ff5-e583d187374c/download/tha_adm2_gista_plyg_v5.zip
$ unzip tha_adm2_gista_plyg_v5.zip
$ cd ../..
```

### Use `ogrinfo` to examine spatial data
[GDAL `ogrinfo`](http://www.gdal.org/ogr_utilities.html) : Lists information about an OGR supported data source.
```
$ ogrinfo data/tha_adm2_gista_plyg_v5/THA_Adm2_GISTA_plyg_v5.shp
INFO: Open of `data/tha_adm2_gista_plyg_v5/THA_Adm2_GISTA_plyg_v5.shp'
      using driver `ESRI Shapefile' successful.
1: THA_Adm2_GISTA_plyg_v5 (Polygon)
```
Use **ogrinfo -so**
Summary Only (-so): supress listing of features, show only the summary information like projection, schema, feature count and extents.
```
$ ogrinfo -so data/tha_adm2_gista_plyg_v5/THA_Adm2_GISTA_plyg_v5.shp THA_Adm2_GISTA_plyg_v5
INFO: Open of `data/tha_adm2_gista_plyg_v5/THA_Adm2_GISTA_plyg_v5.shp'
      using driver `ESRI Shapefile' successful.

Layer name: THA_Adm2_GISTA_plyg_v5
Metadata:
  DBF_DATE_LAST_UPDATE=2017-08-16
Geometry: Polygon
Feature Count: 928
Extent: (97.343358, 5.613038) - (105.636965, 20.465074)
Layer SRS WKT:
GEOGCS["GCS_WGS_1984",
    DATUM["WGS_1984",
        SPHEROID["WGS_84",6378137.0,298.257223563]],
    PRIMEM["Greenwich",0.0],
    UNIT["Degree",0.0174532925199433],
    AUTHORITY["EPSG","4326"]]
PROV_NAMT: String (80.0)
Adm1Name: String (254.0)
Adm1Code: String (254.0)
AMP_NAMT: String (80.0)
Adm2Name: String (254.0)
Adm2Code: String (254.0)
Admin0Name: String (50.0)
Admin0Code: String (2.0)
```

## Geographic Models
### Try ogrinspect
Automate geographic models and import data process with use of the `ogrinspect` management command.
The [`ogrinspect`](https://docs.djangoproject.com/en/2.0/ref/contrib/gis/tutorial/#try-ogrinspect) command introspects a GDAL-supported vector data source (e.g., a shapefile) and generates a model definition and LayerMapping dictionary automatically
```
$ python manage.py ogrinspect [options] <data_source> <model_name> [options]

$ python3 manage.py ogrinspect network_topology/data/THA_Adm2_GISTA_plyg_v5.shp NetworkTopology --srid=4326 --mapping --multi
```

The command produces the following output , which may be copied directly into the **models.py**

```
# This is an auto-generated Django model module created by ogrinspect.
from django.contrib.gis.db import models

class NetworkTopology(models.Model):
    prov_namt = models.CharField(max_length=80)
    adm1name = models.CharField(max_length=254)
    adm1code = models.CharField(max_length=254)
    amp_namt = models.CharField(max_length=80)
    adm2name = models.CharField(max_length=254)
    adm2code = models.CharField(max_length=254)
    admin0name = models.CharField(max_length=50)
    admin0code = models.CharField(max_length=2)
    geom = models.MultiPolygonField(srid=4326)


# Auto-generated `LayerMapping` dictionary for NetworkTopology model
networktopology_mapping = {
    'prov_namt': 'PROV_NAMT',
    'adm1name': 'Adm1Name',
    'adm1code': 'Adm1Code',
    'amp_namt': 'AMP_NAMT',
    'adm2name': 'Adm2Name',
    'adm2code': 'Adm2Code',
    'admin0name': 'Admin0Name',
    'admin0code': 'Admin0Code',
    'geom': 'MULTIPOLYGON',
}
```

### Defining a Geographic Model
Copied the output from the `ogrinspect` command to **models.py** in **network_topology** app.
Note that the **models** module is imported from **django.contrib.gis.db**.

We use a `amp_namt` field to returns the string representation of the model. The default spatial reference system for geometry fields is WGS84 (meaning the SRID is 4326).
```
from django.contrib.gis.db import models

# Create your models here.
class NetworkTopology(models.Model):
    prov_namt = models.CharField(max_length=80)     # provinc_name_th
    adm1name = models.CharField(max_length=254)     # provinc_name_en
    adm1code = models.CharField(max_length=254)     # province_code
    amp_namt = models.CharField(max_length=80)      # amphoe_name_th
    adm2name = models.CharField(max_length=254)     # amphoe_name_en
    adm2code = models.CharField(max_length=254)     # amphoe_code
    admin0name = models.CharField(max_length=50)    # country
    admin0code = models.CharField(max_length=2)     # contry_code
    # GeoDjango-specific: a geometry field (MultiPolygonField)
    geom = models.MultiPolygonField(srid=4326)

    # Returns the string representation of the model.
    def __str__(self):
        return self.amp_namt
```
### Create a database migration
After defining your model, we need to sync it with the database.

* [makemigrations : ](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-makemigrations) Creates new migrations based on the changes detected to your models.
```
$ python manage.py makemigrations
Migrations for 'network_topology':
  network_topology/migrations/0001_initial.py
```

* [sqlmigrate : ](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-sqlmigrate) Prints the SQL statements for a migration
```
$ python manage.py sqlmigrate network_topology 0001
BEGIN;
--
-- Create model NetworkTopology
--
CREATE TABLE "network_topology_networktopology" (
  "id" serial NOT NULL PRIMARY KEY,
  "prov_namt" varchar(80) NOT NULL,
  "adm1name" varchar(254) NOT NULL,
  "adm1code" varchar(254) NOT NULL,
  "amp_namt" varchar(80) NOT NULL,
  "adm2name" varchar(254) NOT NULL,
  "adm2code" varchar(254) NOT NULL,
  "admin0name" varchar(50) NOT NULL,
  "admin0code" varchar(2) NOT NULL,
  "geom" geometry(MULTIPOLYGON,4326) NOT NULL);
CREATE INDEX "network_topology_networktopology_geom_id"
  ON "network_topology_networktopology" USING GIST ("geom");
COMMIT;
```
* [migrate : ](https://docs.djangoproject.com/en/2.0/ref/django-admin/#django-admin-migrate) Synchronizes the database state with the current set of models and migrations.
```
$ python manage.py migrate
Operations to perform:
  Apply all migrations: accidents, admin, auth, contenttypes, network_topology, sessions, world
Running migrations:
  Applying network_topology.0001_initial... OK
```

## Importing Spatial Data

### LayerMapping

## Putting Data on the Map
