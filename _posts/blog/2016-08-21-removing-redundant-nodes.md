---
layout: post
title:  "The How part (5) - Removing redundant nodes from OSM files"
date:   2016-08-21 18:00:00
categories: gsoc open-source marble 
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

The requirement for medium level tiles are a little bit different from those of lower level tiles. The biggest difference being that the medium levels will use openstreetmap data as opposed to the Natural Earth data. Another differnce is that in lower level tiles the major data pre-processing steps were concatenation and conversion since the data was in shapefile format and we our requirement was that of OSM wereas in medium levels the major data-pre processing steps are reduction, simplification, filteration and merging since the openstreetmap data.

As already mentioned in a previous post, openstreetmap describes data at a pretty high resolution. Such resolution is fine for higher levels such as street levels but cannot be suitably used for medium (and also lower levels). Hence data-preprocessing steps such as reduction to a lower resolution, filtering features such as trees and lamp posts which are not visible at medium levels must be removed altogether, and buildings and lanes which are very close must be merged since they appear as a single unit from medium levels.

In order to pre-process data so as to reduce, filter, combine; various tools need to be built which modify the raw openstreetmap data so as to make it suitable for a particular Marble zoom level.

The first tool which I built removes redundant nodes from geographical features. Redundant nodes are the nodes which are just not required at a particualr zoom level since the resolution with which these nodes describe their parent geographic feature exceeds the maximum resolution which is visible at a particular zoom level.

Consider this particular patch of lanes at zoom level 11.

![kpiece1]({{ site.url }}/assets/kpiece1.png)

Now observe the number of nodes with which this patch has been described.

![kpiece1]({{ site.url }}/assets/kpiece2.png)

A small square describes a single node. These squares are colored in a translucent manner so as to depict overlappings. Higher the intensity of the color, greater are the number of overlappings. 





