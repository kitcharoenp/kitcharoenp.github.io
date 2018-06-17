---
layout: post
title: "OpenLayers Rendering KML"
published: true
categories: [gis, openlayers]
---
### Center Map 
Center Map in OpenLayers you could set in [ol.View](https://openlayers.org/en/latest/apidoc/ol.View.html) option name `center`
The initial center for the view. The coordinate system for
the center is specified with the [projection](https://openlayers.org/en/latest/apidoc/ol.proj.html) option. In this case use [fromLonLat](https://openlayers.org/en/latest/apidoc/ol.proj.html#.fromLonLat) method.

```
// thailand [long, lat] = [101, 13]
var centerFromLonLat = ol.proj.fromLonLat([101, 13]);

var map = new ol.Map({
...
  view: new ol.View({
    center: centerFromLonLat,
    zoom: 6
  })
...
```
