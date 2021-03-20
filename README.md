# link-data-to-line
Basic GRASS GIS routines / pseudocode for linking data sets to (for our purposes) a valley centerline

***These are the basic shell commands that you would type into a computer; Python scripts may be written later, based off of these.***

## To start

Define a GRASS GIS region with your chosen projection. (For the group for which I am writing this: WGS 84, UTM 15N.)

Need help? Here's the GRASS GIS reference manual full index: https://grass.osgeo.org/grass79/manuals/full_index.html.

## Import data
```sh
# Raster
# I always used to use "r.in.gdal". But r.import reprojects on the fly!
# GRASS stores data inside its internal directory structure.
r.import in=file_path out=your_raster_name_in_GRASS_location
# Vector
# Likewise, replacing "v.in.ogr"
v.import in=file_path out=your_vector_name_in_GRASS_location
```

## Raster + Vector (hand-mapped river terraces)

```sh
# First, set the region to the imported raster.
# This sets where computations are done
g.region rast=your_raster_name
```
