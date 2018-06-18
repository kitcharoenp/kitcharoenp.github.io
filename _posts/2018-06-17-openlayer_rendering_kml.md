---
layout: post
title: "OpenLayers Rendering KML"
published: false
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

### D3 Integration
* [earthquake](https://gist.github.com/mbertrand/5218300#file-quakes-json)
* [D3 Coding](http://blockbuilder.org/)
* [26 Map Examples](https://react.rocks/tag/Map)
* [Volume Analytics Chaos Control](https://volumeintegration.com/category/data-visualization/geospatial/)
* [A responsive sidebar with tabs for Leaflet, OpenLayers, ..](https://github.com/Turbo87/sidebar-v2)
* [Build Apps With Boundless SDK and OpenGeo ](https://boundlessgeo.com/2014/07/build-apps-with-boundless-sdk/)
* [Build a store locator using Mapbox ](https://www.mapbox.com/help/building-a-store-locator/)
* [bootstrap-viewer-template](https://github.com/jumpinjackie/bootstrap-viewer-template)
* [Top 19 geovisualization tools](http://geoawesomeness.com/top-19-online-geovisualization-tools-apis-libraries-beautiful-maps/)
