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

## Raster (optionally, + Vector) data to vector points (hand-mapped river terraces)

*You may skip this entire step if you already have vector point data.*

### If you have raster data with a vector mask

```sh
# First, set the region to the imported raster.
# This sets where computations are done and the resolution
g.region rast=your_raster_name
# Then, convert the vector to a raster that will be used to
# select a subset of the DEM for analysis.
# This is not the only way to accomplish this,but is the most straightforward.
# The last two entries instruct it to give a value of "1" to all of the
# raster cells where there is a vector polygon; otherwise, these cells
# will have a NULL value.
v.to.rast input=your_vector_name output=terraces use=val val=1
# Next, place the elevations of the terraces into a new raster map
r.mapcalc "z_terraces = your_DEM * terraces"
```

### In all cases starting with raster data

*Continue from the above if you have a vector data set to clip the raster, or skip to this step if you do not.*

```
# Our next goal is to link each of these raster cells to the centerline.
# In order to do this, we must first convert each grid cell to a vector
# point.
# You can use the same name for raster and vector data sets;
# they are stored separately.
# This will store the elevation in a column named "z"
r.to.vect in=z_terraces out=z_terraces type=point column=z
```

## Connect vector points to distance along line

```sh
# Import your vector line (river centerline in this example)
# using v.import (see above) if you have not done so yet.
# After this, v.distance will find the closest point along the line
# and will give this distance ("along") as a value within the attribute table
# of the point data
# You could add "to_x" and "to_y" to also obtain the E,N coordinates
# of the point along the line to which you are matching the terrace
# First, then, we need to add the column.
v.db.addcolumn map=z_terraces column="valley_dist double precision"
Then, load the distance along the centerline to the column
v.distance from=z_terraces to=your_vector_line upload=to_along column=valley_dist
```
