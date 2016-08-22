---
layout: post
title:  "The How part (6) - Concatenating OSM way chunks "
date:   2016-08-22 21:00:00
categories: gsoc open-source marble 
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

As of now, If you load an openstreetmap XML file containing linestrings such as highways, lanes or streets, in Marble and zoom to level 11, you will find that the highways or streets are discountinous and appear as broken. 


You will find the roads/streets as broken and discontinous

![roads_broken]({{ site.url }}/assets/ak1.png)

Instead of getting continous roads

![road_joined]({{ site.url }}/assets/ak2.png)

One of the primary reason for this discontinuity, as told by my mentor Torsten Rahn, is that often a particular highway/lane/street is described using multiple contiguous OSM way elements instead of a single OSM way element. Marble treats each of these specific way element as a linestring object. However Marble omits rendering any objects which are smaller than 2px, in order to imporve rendering performance.  Due to this, many of the OSM way elements, which are smaller than 2px don't get rendered. Hence the street appears broken since only those of its OSM way elements are rendered which are larger than 2px.

One of the reasons which I can think of and which justifies this highway description using multiple elements is that a street might be called by a certain name for a specific stretch and might be called by some other different name for the remaining stretch. However, at level 11 we are mostly concerned with the type of highways (primary, secondary, motorway , residential) rather than the specifics such as names.

Usually, the multiple OSM way elements of a single highway share the first and the last node ID references. For example consider         <1n...2n> as an OSM way element where 1n and 2n corresponds to the first and last node IDs of the way. A highway described by an ordered list of node IDs 1 to 56 can then usually be represented by the following OSM way elements <1...5>, <5...13>, <13...28>, <28...36>, <36...56>

I exploited this way of representation to create a way concatenating module in the existing osm-module tool. For the above example, the module would concat all the 5 OSM way elements into a single OSM way element <1...56>

```
osm-simplify -t highway=* -w input.osm
```

The above command concatenates all the highways present in the input file and produces a new osm file as output.

Apart from solving the problem of discontinuity, way concatenation also results in data reduction since it is eliminating redundant node references and way elements. This data reduction in turn results in faster file parsing as well as improved rendering performance since now to render a single stretch of highway one only needs to create and render a single linestring object as opposed to multiple linestring objects.

The tricky part of coding this OSM way concatenator is actually coming up with an algorithm which concatenates all the linestrings present in an input file. Finally I and my mentors Dennis Nienhüser and Torsten Rahn were able to come up with a working algorithm for concatenating osm ways of a file in reasonable time (approximately O(n) time).

The algorithm involves a made up data structure called WayChunk which basically is a list of contiguous ways. It also stores a GeoDataVisualCategory which represents the type of linestring. For example in case of highways GeoDataVisualCategory will contain the kind of highway, wether it is a motorway, primary, secondary or a residential type of highway.

It also involves a multi hash map which has nodeID's as keys and WayChunk's as the values.







