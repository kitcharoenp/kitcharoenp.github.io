---
layout: post
title: "Django with OpenLayers Rendering KML"
published: false
categories: [gis, openlayers]
---
### Set Visible Layers
* Use zoom level
See [Moveend Event Examples](https://openlayers.org/en/latest/examples/moveend.html). In
this code use the layer group

```
function onMoveEnd(evt) {
      var map = evt.map;
      var zoom = map.getView().getZoom();
      mLayers = map.getLayers();
      mLayers.forEach(function(element) {
        // get layer title is 'Overlays'
        if( element.get('title') == 'Overlays')
        {
          mOverlays = element.getLayers();
          mOverlays.forEach(function(overlay) {
            // get layer title is 'Eastern'
            if( overlay.get('title') == 'Eastern')
            {
              if( zoom < 9){
                overlay.setVisible(false);
              }
              else{
                overlay.setVisible(true);
              }
            }
          })
        }
      });
    }
    map.on('moveend', onMoveEnd);
```

* [Use minResolution/maxResolution](https://stackoverflow.com/questions/15744056/show-hide-kml-on-defined-zoom-levels)
Set the minimum resolution at which the layer is visible.

```
var vectorKmlEastern = new ol.layer.Vector({
    title: 'Eastern',
    minResolution: 0,
    maxResolution: 200,
    source: new ol.source.Vector({
        url: kml_east,
        format: new ol.format.KML({
            extractStyles: false,
        })
    }),
    style: styleFunctionKml
});
```

### Style Geometries by Type and Name
```
var geometryType = feature.getGeometry().getType();
var name = feature.get('name');
...
if (geometryType == 'Point') {
  if (name.startsWith('BJ')) {
    style = stylePointJointMany;
  }
...
if (geometryType == 'MultiLineString' || geometryType == 'LineString') {
  style = new ol.style.Style({
    stroke: new ol.style.Stroke({
      color: 'rgba(151, 206, 104, 0.4)',
      width: 5,
    })
  });
}
```

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
* [Union Geometries inside the Spatial Database](http://workshops.boundlessgeo.com/postgis-spatialdbtips/click-union.html)
