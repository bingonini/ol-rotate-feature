---
redirect_from:
  - /ol3-rotate-feature/
---
[![Build Status](https://travis-ci.org/ghettovoice/ol-rotate-feature.svg?branch=master)](https://travis-ci.org/ghettovoice/ol-rotate-feature)
[![Coverage Status](https://coveralls.io/repos/github/ghettovoice/ol-rotate-feature/badge.svg?branch=master)](https://coveralls.io/github/ghettovoice/ol-rotate-feature?branch=master)
[![GitHub tag](https://img.shields.io/github/tag/ghettovoice/ol-rotate-feature.svg)](https://github.com/ghettovoice/ol-rotate-feature/releases)
[![view on npm](http://img.shields.io/npm/v/ol-rotate-feature.svg)](https://www.npmjs.org/package/ol-rotate-feature)
[![License](https://img.shields.io/github/license/ghettovoice/ol-rotate-feature.svg)](https://github.com/ghettovoice/ol-rotate-feature/blob/master/LICENSE)

# Rotate feature interaction for OpenLayers

Plugin adds interaction that allows to rotate vector features around some anchor.

### [Demo](https://ghettovoice.github.io/ol-rotate-feature/demo.html)

## Installation

Install it thought NPM or Bower:

```shell
npm install openlayers ol ol-rotate-feature
bower install openlayers ol-rotate-feature
```

Or download the latest version archive and add it with script tag:

```html
<script src="/js/openlayers/dist/ol.js"></script>
<script src="/js/ol-rotate-feature/dist/bundle.min.js"></script>
```

### Note
**Plugin is available in 2 versions: as UMD module and as ES2015 module:**
- **UMD version (`dist/bundle[.min].js`) should be used with [openlayers](https://www.npmjs.com/package/openlayers) package.
  You can install `ol` package as dev dependency to suppress NPM warning about required peer dependencies.**
- **ES2015 version (`dist/bundle.es.js`) should be used with [ol](https://www.npmjs.com/package/ol) package (you should
  install it manually). You can install `openlayers` package as dev dependency to suppress NPM warning about required peer dependencies.**

## Usage

Plugin may be used as UMD module or ES2015 module:

```js
// Use as ES2015 module (based on NPM package `ol`)
import Map from 'ol/map'
...
import RotateFeatureInteraction from 'ol-rotate-feature'

// Use as UMD module (based on NPM package `openlayers`)
const ol = require('openlayers')
...
const RotateFeatureInteraction = require('ol-rotate-feature')
```

In Browser environment you should add **script** tag pointing to UMD module after OpenLayers js files.
```html
<script src="/js/openlayers/dist/ol.js"></script>
<script src="/js/ol-rotate-feature/dist/bundle.min.js"></script>
<script>
  // now plugin is available at `ol.interaction.RotateFeature` field
</script>
```

### Options

| Option         | Type                                                                                               | Description                                                                                                                 |
|:---------------|:---------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------|
| features       | _ol.Collection<ol.Feature>_                                                                        | The features the interaction works on. **Required**.                                                                        |
| style          | _ol.style.Style &#124; Array&lt;ol.style.Style&gt; &#124; ol.style.StyleFunction &#124; undefined_ | Style of the overlay with interaction helper features.                                                                      |
| angle          | _number &#124; undefined_                                                                          | Initial angle in radians (positive is counter-clockwise), applied for features already added to collection. Default is `0`. |
| anchor         | _number[] &#124; ol.Coordinate &#124; undefined_                                                   | Initial anchor coordinate. Default is center of features extent.                                                            |

### Methods

```js
// Set current angle of interaction features.
RotateFeatureInteraction.prototype.setAngle(angle : number)
```

```js
// Returns current angle of interaction features.
RotateFeatureInteraction.prototype.getAngle() : number
```

```js
// Set current anchor position.
RotateFeatureInteraction.prototype.setAnchor(anchor? : number[] | ol.Coordinate)
```

```js
// Returns current anchor position.
RotateFeatureInteraction.prototype.getAnchor() : number[] | ol.Coordinate | undefined 
```

### Events

All events triggered by the interaction are instances of `RotateFeatureEvent`.

##### Members

- **features**    _ol.Collection_     The features being rotated.
- **angle**       _number_            Current angle in radians.
- **anchor**      _ol.Coordinate_     Current anchor position.

| Event       | Arguments            | Description                          |
|:------------|:---------------------|:-------------------------------------|
| rotatestart | _RotateFeatureEvent_ | Triggered upon feature rotate start. |
| rotating    | _RotateFeatureEvent_ | Triggered upon feature rotating.     |
| rotateend   | _RotateFeatureEvent_ | Triggered upon feature rotation end. |

### Example usage:

```js
import Map from 'ol/map'
import View from 'ol/view'
import TileLayer from 'ol/layer/tile'
import VectorLayer from 'ol/layer/vector'
import OSMSource from 'ol/source/osm'
import VectorSource from 'ol/source/vector'
import Feature from 'ol/feature'
import Point from 'ol/geom/point'
import LineString from 'ol/geom/linestring'
import Polygon from 'ol/geom/polygon'
import Select from 'ol/interaction/select'
import RotateFeature from 'ol-rotate-feature'

const point = new Feature({
  name: 'point',
  geometry: new Point([ 2384267.0573564973, 7557371.884852641 ])
})
const line = new Feature({
  name: 'line',
  geometry: new LineString([ [ -603697.2100018249, -239432.60826165066 ], [ 4190433.20404443, 2930563.8287811787 ] ])
})
const polygon = new Feature({
  name: 'polygon',
  geometry: new Polygon([ [
    [ -14482348.171434438, 6661491.741627443 ],
    [ -9541458.663080638, 6221214.458704827 ],
    [ -11473786.738129886, 3300708.4819848104 ],
    [ -14482348.171434438, 6661491.741627443 ]
  ] ])
})

const map = new Map({
  view: new View({
    center: [ 0, 0 ],
    zoom: 2
  }),
  layers: [
    new TileLayer({
      source: new OSMSource()
    }),
    new VectorLayer({
      source: new VectorSource({
        projection: 'EPSG:33857',
        features: [ point, line, polygon ]
      })
    })
  ],
  target: 'map',
  projection: 'EPSG:3857'
})

const select = new Select()
select.getFeatures().extend([ point, line, polygon ])

const rotate = new RotateFeature({
  features: select.getFeatures(),
  anchor: [ 0, 0 ],
  angle: -90 * Math.PI / 180
})

rotate.on('rotatestart', evt => console.log('rotate start', evt))
rotate.on('rotating', evt => console.log('rotating', evt))
rotate.on('rotateend', evt => console.log('rotate end', evt))

map.addInteraction(select)
map.addInteraction(rotate)
```

Example of usage in Browser environment in [test/umd.html](https://github.com/ghettovoice/ol-rotate-feature/tree/master/test/umd.html).

Getting total angle or last anchor coordinates after rotation:

```js
rotate.on('rotateend', evt => {
    // get total angle in degrees
    console.log(evt.angle + ' is '+ (-1 * evt.angle * 180 / Math.PI ) + '°')
    // get last anchor coordinates
    console.log(evt.anchor)
})
```

## License

MIT (c) 2016-2017, Vladimir Vershinin
