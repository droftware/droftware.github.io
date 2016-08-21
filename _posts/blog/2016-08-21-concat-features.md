---
layout: post
title:  "The How part (3) - Concatenating the Natural Earth geographical features into a single OSM file"
date:   2016-08-21 16:00:00
categories: gsoc open-source marble kde osgeo osm 
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

Till now, the polyshp2osm tool, which I had modified to support various kinds of geometries as well as OSM tag-mappings for Natural Earth metadata, supported conversion of a single shapefile into OSM.

```
python3 ~/a/marble/tools/shp2osm/polyshp2osm.py ne_110m_rivers_lake_centerlines.shp
```

The above command will convert the given shp file containing rivers and lakes into its OSM equivalent.

One of the major aims of my project was to create a tool for automatically generating the OSM tiles for lower zoom levels using Natural Earth data. In my previous post, I described how I added styling for many geographical features so as to enable rendering of many Natural Earth features such as reefs and bathymetries. Now before creating a tool which can output the tiles, I need a tool which can concatenate all the different Natural Earth features such as roads, rails, rivers, reefs into one single OSM file which then can be broken down into tiles for lower zoom levels. Basically I need to modify the tool so that it can take multiple shp files as input and then produces a combined OSM file.

Now the straightforward way of doing this is to just concat the XML elements produced for different shp files. This will obviously work but will contain way too much redundant data especially in the form of redundant nodes. I solved this redundancy by using a dictionary which maps the (longitude, latitude) of a node to a unique node ID. Now whenever the tool iterates over the nodes of a polygon or a linestring in the tool, it is checked whether that node is already present in the dictionary or not. If it is not present, we assign a new ID to the node and add it to the dictionary, if it is present in the dictionary , we use the node ID of the existing node instead of creating a redundant node having the same longitude,latitude but a different ID.

Now we can give multiple shp files as input to the tool and it will generate a single OSM file containing all the features of the input shp file without any redundant node data.

```
python3 ~/a/marble/tools/shp2osm/polyshp2osm.py ne_50m_land ne_50m_geographic_lines ne_50m_lakes ne_50m_glaciated_areas
```

The above command will concatenate the Natural Earth shp files containing land, geographic line, lakes and glaciated areas into a single OSM file.
