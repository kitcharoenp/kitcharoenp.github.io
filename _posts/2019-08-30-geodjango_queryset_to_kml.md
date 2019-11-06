---
layout: post
title: "GeoDjango QuerySet to KML"
published: true
categories: [gis]
---
### **models/point_models.py**

```python
from django.contrib.gis.db import models

# Create your models here.

class PointManager(models.Manager):
    pass

class Point(models.Model):
    """
    Model representing a geometry point
    """
    objects = PointManager()

    POINT_TYPE = (
        ('LOSS', 'LOSS'),
        ('ODF', 'ODF'),
        ('OFC_CLOSURE', 'OFC_CLOSURE'),
        ('LOOP', 'LOOP'),
        ('POLE', 'POLE'),
        ('PB', 'PB'),
        ('MAINHOLE', 'MAINHOLE'),
        ('RISER', 'RISER'),
        ('CUSTOMER', 'CUSTOMER'),
        ('NODE', 'NODE'),
        ('EQUIPMENT', 'EQUIPMENT'),
        ('PATCH', 'PATCH'),
    )

    created_at = models.DateTimeField(auto_now_add=True, blank=True)
    last_modified_at = models.DateTimeField(auto_now=True, blank=True)
    inactive_at = models.DateTimeField(blank=True, null=True)

    active = models.BooleanField(default=True)
    circuit_id = models.CharField(max_length=64, null=True)
    description = models.TextField(
        max_length=1000,
        blank=True,
        null=True,
        help_text="Enter a brief description of the point"
    )

    geom = models.PointField(srid=4326, null=True,)
    longitude = models.FloatField(null=True)
    latitude = models.FloatField(null=True)
    layer_name = models.CharField(max_length=384,blank=True,null=True)
    name = models.CharField(max_length=384)
    POINT_CATEGORY = (
        ('AERIAL', 'AERIAL'),
        ('UNDERGROUND', 'UNDERGROUND'),
    )
    point_category = models.CharField(
        max_length=32,
        choices=POINT_CATEGORY,
        default='AERIAL',
    )
    point_type = models.CharField(
        max_length=16,
        choices=POINT_TYPE,
        default='OFC_CLOSURE',
    )
    POINT_OPERATOR = (
        ('ITEL', 'ITEL'),
        ('NBTC', 'NBTC'),
        ('PEA', 'PEA'),
        ('MEA', 'MEA'),
    )
    operator = models.CharField(
        max_length=16,
        choices=POINT_OPERATOR,
        default='ITEL',
    )
    service_order = models.CharField(max_length=64, null=True)
    validated = models.BooleanField(default=False)
    work_order = models.CharField(max_length=64, null=True)

    def __str__(self):
        """
        String for representing the Model object.
        """
        return f'{self.pk} ({self.name})'

    def natural_key(self):
        return (self.pk, self.name)
```

### **models/line_models.py**
```python
from django.contrib.gis.db import models

class Line(models.Model):
    """
    Model representing a geometry line string
    """
    objects = LineManager()

    LINE_TYPE = (
        ('ADSS', 'ADSS'),
        ('DROPWIRE', 'DROPWIRE'),
        ('FIG8', 'FIG8'),
        ('SELFSUPPORT', 'SELFSUPPORT'),
        ('ARSS', 'ARSS'),
    )

    created_at = models.DateTimeField(auto_now_add=True, blank=True)
    last_modified_at = models.DateTimeField(auto_now=True, blank=True)
    inactive_at = models.DateTimeField(blank=True, null=True)

    active = models.BooleanField(default=True)
    circuit_id = models.CharField(max_length=64, null=True, blank=True,)
    core = models.PositiveSmallIntegerField(default=2,  validators=[MaxValueValidator(256)])
    description = models.TextField(
        max_length=1000,
        blank=True,
        null=True,
        help_text="Enter a brief description of the line"
    )

    geom = models.LineStringField(srid=4326, null=True, blank=True,)
    length = models.FloatField(default=0.00)
    layer_name = models.CharField(max_length=254, blank=True, null=True,)

    LINE_CATEGORY = (
        ('AERIAL', 'AERIAL'),
        ('UNDERGROUND', 'UNDERGROUND'),
    )
    line_category = models.CharField(
        max_length=32,
        choices=LINE_CATEGORY,
        default='AERIAL',
    )

    LINE_STATUS = (
        ('USED', 'USED'),
        ('DAMAGE', 'DAMAGE'),
        ('UNDER_CONSTRUCTION', 'UNDER_CONSTRUCTION'),
    )
    line_status = models.CharField(
        max_length=32,
        choices=LINE_STATUS,
        default='USED',
    )

    line_type = models.CharField(
        max_length=16,
        choices=LINE_TYPE,
        blank=True,
    )

    name = models.CharField(max_length=254)
    """
    use get-or-create create m2m2 related_cores
    https://docs.djangoproject.com/en/2.1/ref/models/querysets/#get-or-create
    """

    LINE_OPERATOR = (
        ('ITEL', 'ITEL'),
        ('NBTC', 'NBTC'),
        ('PEA', 'PEA'),
        ('MEA', 'MEA'),
    )
    operator = models.CharField(
        max_length=16,
        choices=LINE_OPERATOR,
        default='ITEL',
    )
    otdr_len = models.FloatField(default=0.00)
    service_order = models.CharField(max_length=64, null=True, blank=True,)
    source_poi = models.ForeignKey(
        'Point',
        related_name='source_poi',
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
    )
    target_poi = models.ForeignKey(
        'Point',
        related_name='target_poi',
        on_delete=models.SET_NULL,
        null=True,
        blank=True,
    )
    validated = models.BooleanField(default=False)
    work_order = models.CharField(max_length=64, null=True, blank=True,)

    def __str__(self):
        """
        String for representing the Model object.
        """
        return self.name

    def natural_key(self):
        return (self.name)
```

### **models/thaadm1_models.py**
```python
from django.contrib.gis.db import models


class THAAdm1(models.Model):

    THAADM1_REGION = (
        ('CENTRAL', 'CENTRAL'),
        ('EAST', 'EAST'),
        ('METRO', 'METRO'),
        ('UP_NORTH', 'UP_NORTH'),
        ('DOWN_NORTH', 'DOWN_NORTH'),
        ('UP_NORTHEAST', 'UP_NORTHEAST'),
        ('DOWN_NORTHEAST', 'DOWN_NORTHEAST'),
        ('SOUTH', 'SOUTH'),
        ('WEST', 'WEST'),
    )

    region = models.CharField(
        max_length=16,
        choices=THAADM1_REGION,
        blank=True,
        null=True,
    )

    iso = models.CharField(max_length=254)
    prov_namt = models.CharField(max_length=80)
    adm1name = models.CharField(max_length=254)
    adm1code = models.CharField(max_length=254)
    adm0name = models.CharField(max_length=50)
    admin0code = models.CharField(max_length=2)
    geom = models.MultiPolygonField(srid=4326)
    short_label = models.CharField(max_length=2, blank=True, null=True,)

    class Meta:
        ordering = ['-prov_namt']

    def __str__(self):
        """
        String for representing the Model object.
        """
        return f'{self.adm1code} :=:  {self.prov_namt}'
```

### **views/geom_export_to_kml_views.py**

```python
#importing the object django.conf.settings
from django.conf import settings
from django.core.files.storage import FileSystemStorage
from django.db.models import Q

from optnet.models import Line, Point, THAAdm1
import os
import json
import requests
import xml.dom.minidom

def create_point_data_element(kml_doc, point, ext_element):
    point_dict=point.__dict__
    point_fields = ['id', 'name', 'point_category',
              'point_type', 'operator', 'service_order', 'work_order', 'description']

    # Loop through the point attribute and create a element for every field that has a value.
    for key in point_fields:
        if point_dict[key]:
            data_element = kml_doc.createElement('Data')
            data_element.setAttribute('name', key)

            value_element = kml_doc.createElement('value')
            data_element.appendChild(value_element)
            value_text = kml_doc.createTextNode(str(point_dict[key]))
            data_element.appendChild(value_text)

            ext_element.appendChild(data_element)
    return ext_element

def create_placemark_point(kml_doc, point):

    # <Placemark> ...</Placemark>
    placemark_element = kml_doc.createElement('Placemark')
    ext_element = kml_doc.createElement('ExtendedData')
    placemark_element.appendChild(ext_element)

    # <name> ...</name>
    name_element = kml_doc.createElement('name')
    name_element.appendChild(kml_doc.createTextNode(str(point.name)))
    placemark_element.appendChild(name_element)

    # <ExtendedData> ...</ExtendedData>
    create_point_data_element(kml_doc, point, ext_element)

    # <Point> ...</Point>
    point_element = kml_doc.createElement('Point')
    placemark_element.appendChild(point_element)

    x = point.geom.x
    y = point.geom.y
    coordinates = str(x)+','+str(y)

    # <coordinates> ...</coordinates>
    coor_element = kml_doc.createElement('coordinates')
    coor_element.appendChild(kml_doc.createTextNode(coordinates))
    point_element.appendChild(coor_element)

    return placemark_element

def create_line_data_element(kml_doc, line, ext_element):
    line_dict=line.__dict__
    line_fields = ['id', 'name', 'line_type', 'length', 'line_category',
                    'core', 'service_order', 'work_order', 'description', 'operator']

    # Loop through the point attribute and create a element for every field that has a value.
    for key in line_fields:
        if line_dict[key]:
            data_element = kml_doc.createElement('Data')
            data_element.setAttribute('name', key)

            value_element = kml_doc.createElement('value')
            data_element.appendChild(value_element)
            value_text = kml_doc.createTextNode(str(line_dict[key]))
            data_element.appendChild(value_text)

            ext_element.appendChild(data_element)
    return ext_element

def style_linestring(kml_doc, placemark_element):
    # <Style> ...</Style>
    style_element = kml_doc.createElement('Style')
    placemark_element.appendChild(style_element)

    # <LineStyle> ...</LineStyle>
    line_style_element = kml_doc.createElement('LineStyle')
    style_element.appendChild(line_style_element)

    # <color> ...</color>
    color_element = kml_doc.createElement('color')
    color_element.appendChild(kml_doc.createTextNode('7faaff7f'))
    line_style_element.appendChild(color_element)

    # <width> ...</width>
    width_element = kml_doc.createElement('width')
    width_element.appendChild(kml_doc.createTextNode('6'))
    line_style_element.appendChild(width_element)

    return placemark_element

def create_placemark_linestring(kml_doc, line):

    # <Placemark> ...</Placemark>
    placemark_element = kml_doc.createElement('Placemark')

    ext_element = kml_doc.createElement('ExtendedData')
    placemark_element.appendChild(ext_element)

    # style line string
    placemark_element = style_linestring(kml_doc, placemark_element)

    # <name> ...</name>
    name_element = kml_doc.createElement('name')
    name_element.appendChild(kml_doc.createTextNode(str(line.name)))
    placemark_element.appendChild(name_element)

    # <ExtendedData> ...</ExtendedData>
    create_line_data_element(kml_doc, line, ext_element)

    # <LineString> ...</LineString>
    line_element = kml_doc.createElement('LineString')
    placemark_element.appendChild(line_element)

    coordinates = line.geom.kml.replace("<LineString><coordinates>", "").replace("</coordinates></LineString>", "")

    # <coordinates> ...</coordinates>
    coor_element = kml_doc.createElement('coordinates')
    coor_element.appendChild(kml_doc.createTextNode(coordinates))
    line_element.appendChild(coor_element)

    return placemark_element

def create_kml_doc(adm1):
    """
    This tutorial uses code from Geocoding Addresses for Use in KML.
    https://developers.google.com/kml/articles/geocodingforkml
    https://developers.google.com/kml/articles/csvtokml
    """
    # This constructs the KML document from the CSV file.
    kml_doc = xml.dom.minidom.Document()

    kml_element = kml_doc.createElementNS('http://earth.google.com/kml/2.2', 'kml')
    kml_element.setAttribute('xmlns','http://earth.google.com/kml/2.2')
    kml_element = kml_doc.appendChild(kml_element)
    document_element = kml_doc.createElement('Document')
    document_element = kml_element.appendChild(document_element)

    # get points within adm1 geom
    points_in_adm1 = Point.objects.filter(geom__within=adm1.geom)
    for p in points_in_adm1:
        placemark_element = create_placemark_point(kml_doc, p)
        document_element.appendChild(placemark_element)


    # get lines within or intersects adm1 geom
    lines_in_adm1 = Line.objects.filter(
        Q(geom__within=adm1.geom) | Q(geom__intersects=adm1.geom))

    for line in lines_in_adm1:
        placemark_element = create_placemark_linestring(kml_doc, line)
        document_element.appendChild(placemark_element)

    path = os.path.join(settings.MEDIA_ROOT, 'kml/')
    adm1_name = str(adm1.adm1name).replace(" ", "")
    file_name = path+str(adm1.adm1code)+'_'+adm1_name+'.kml'

    with open(file_name, "wb") as outfile:
        outfile.write(kml_doc.toprettyxml(encoding="UTF-8"))

def create_kml_doc_by_adm1():
    adm1_all = THAAdm1.objects.all()
    for adm1 in adm1_all:
        create_kml_doc(adm1)

```
