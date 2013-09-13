reproject
=========

Takes GeoJSON in one projection/CRS and transforms, or reprojects, it to another.

## cli

install:

    $ npm install -g reproject

use:

    $ echo '{"type":"Point","coordinates":[319180, 6399862]}' | reproject --crs-defs=crs-defs.json --from=EPSG:3006 --to=EPSG:4326

A sample file of CRS definitions, crs-defs.json, is supplied. Its contents is a dictionary of CRS names to Proj4 definitions.

reproject can be used together with for example [wellknown](https://github.com/mapbox/wellknown/) and [geojsonio-cli](https://github.com/mapbox/geojsonio-cli/):

    $ echo "POINT(319180 6399862)" | wellknown | reproject --crs-defs=crs-defs.json --from=EPSG:3006 --to=EPSG:4326 | geojsonio

Sometimes your data has reversed the axis order such that x has become y and vice versa. reproject can help you get them in the correct order before transforming by using the **--reverse** argument:

    $ echo '{"type":"Point","coordinates":[6399862, 319180]}' | reproject --reverse --crs-defs=crs-defs.json --from=EPSG:3006 --to=EPSG:4326

## usage

reproject currently only works with node.js, since proj4node, which reproject relies on, does not play nice with
for example browserify. Installation is easy with npm:

    npm install reproject

## api

### reproject(geojson, from, to, crss)

Reprojects the given GeoJSON from the CRS given in **from** to the CRS given in **to**.

The from and to arguments can either be a proj4node projection object, or a string containing a CRS name. In
the case of a CRS name, the proj4node projection instance is looked up using the **crss** argument. **crss**
is assumed to be a dictionary of projection names to proj4node objects.

If from is left undefined or null, the CRS will be detected from the GeoJSON's crs property and looked up in the
**crss** dictionary.

### toWgs84(geojson, from, crss)

Shortcut equivalent to

```js
reproject(geojson, from, proj4node.WGS84, crss)
```

### detectCrs(geojson, crss)

Detects the CRS defined in the given GeoJSON and returns the corresponding proj4node projection instance from
crss. If no CRS is defined in the GeoJSON, or the defined CRS isn't present in **crss**, an error is thrown.

### reverse(geojson)

Reverses the axis order of the coordinates in the given GeoJSON, such that x becomes y and y becomes x.
