---
layout: post
title:  "The How part (5) - Removing redundant nodes from OSM files"
date:   2016-08-21 21:00:00
categories: gsoc open-source marble kde osgeo osm
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

The requirement for medium level tiles are a little bit different from those of lower level tiles. The biggest difference being that the medium levels will use openstreetmap data as opposed to the Natural Earth data. Another difference is that in lower level tiles the major data pre-processing steps were concatenation and conversion since the data was in shapefile format and the requirement was that of OSM whereas in medium levels the major data-pre processing steps are reduction, simplification, filtration and merging.

As already mentioned in a previous post, openstreetmap describes data at a pretty high resolution. Such resolution is fine for higher levels such as street levels but cannot be suitably used for medium (and also lower levels). Hence data-preprocessing steps such as reduction to a lower resolution, filtering features such as trees and lamp posts which are not visible at medium levels , and merging of buildings and lanes which are very close to each other.

In order to pre-process data so as to reduce, filter, combine; various tools need to be built which modify the raw openstreetmap data so as to make it suitable for a particular Marble zoom level.

The first tool which I built removes redundant nodes from geographical features present in the input OSM file. Redundant nodes are the nodes which are just not required at a particular zoom level since the resolution with which these nodes describe their parent geographic feature exceeds the maximum resolution which is visible at a particular zoom level.

Consider this particular patch of lanes at zoom level 11.

![kpiece1]({{ site.url }}/assets/kpiece1.png)

Now observe the number of nodes with which this patch has been described.

![kpiece1]({{ site.url }}/assets/kpiece2.png)

A small square describes a single node. These squares are colored in a translucent manner so as to depict overlappings. Higher the intensity of the color, greater are the number of overlappings.

As you can clearly see, these many nodes are not required for proper rendering of OSM data at medium zoom levels. In order to overcome this problem, I wrote a node reducing module. This module is a part of osm-simplify which is an OSM data preprocessor jointly being developed by Dávid Kolozsvári and me which supports many different kinds of OSM data preprocessing such as clipping, tiling, way-merging(other simplification tools are still being developed)


```
osm-simplify -z 11 -n input.osm
```

The above command removes the redundant nodes according to the resolution of level 11 of Marble and produces a new reduced OSM file as output.

The underlying algorithm of this node reduction module is pretty simple

* Create a new empty list of nodes called reducedLine
* nodeA = first node of the linestring, ring, or a polygon which is under consideration.
* add nodeA to reducedLine
* Iterate from the second to the second last node (so as to retain the first and last nodes) of a linestring, ring,  or a polygon
  * If the great circle distance between the nodeA and the currentNode is greater than the minimum resolution defined for that level
  * Then add currentNode to reducedLine and change value of nodeA to that of currentNode
* Add the last node to reducedLine
* return reducedLine

This simple tool results in significant removal of redundant nodes. The final goal is that this node reduction module along with other modules of osm-simplify tool will result in a significant size reduction of the OSM data for a particular zoom level resulting in improved rendering performance of Marble, without compromising much on the visual quality of the rendered data.

In the next post, I will describe about way-merging module as well as do a little bit of objective comparison of the reduction in size of OSM data caused by these modules.

