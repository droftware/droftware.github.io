---
layout: post
title:  "The How part (6) - Concatenating OSM way chunks "
date:   2016-08-21 21:00:00
categories: gsoc open-source marble 
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

As of now, If you load an openstreetmap XML file containing linestrings such as highways, lanes or streets, in Marble and zoom to level 11, you will find that the highways or streets are discountinous and appear as broken. 


You will find the roads/streets as broken and discontinous
![roads_broken]({{ site.url }}/assets/ak1.png)

Instead of getting continous roads
![road_joined]({{ site.url }}/assets/ak2.png)

One of the primary reason for this discontinuity, as told by my mentor Torsten Rahn, is that often a particular highway/lane/street is described using multiple continous OSM way elements instead of a single OSM way element. Marble treats each of these specific way element as a linestring object. However Marble omits rendering any objects which are smaller than 2px, in order to imporve rendering performance.  Due to this, many of the OSM way elements, which are smaller than 2px don't get rendered. Hence the street appears broken since only those of its OSM way elements are rendered which are larger than 2px.

One of the reasons which I can think of and which justifies this description using multiple elements is that a street might be called by a certain name for a specific stretch and might be called by some other different name for the remaining stretch. However, at level 11 we are mostly concerned with the type of highways (primary, secondary, motorway , residential) rather than the specifics such as names.

Usually, the multiple OSM way elements of a single highway share the first and the last node ID references. For example consider <firstId...lastId> as an OSM way element where the first and last ID corresponds to the first and last node IDs of the way. A highway described by an ordered list of node IDs 1 to 56 can then usually be represented by the following OSM way elements <1...5>, <5...13>, <13...28>, <28...36>, <36...56>
I exploited this way of representation to create a way concatenator. 

```
osm-simplify -t highway=* -w karl.osm
```

In order to circumvent this, I coded a module which concatenates all the multiple continous OSM way elements of a highway to produce a single OSM way element. Apart from solving the above stated problem, it also results in data reduction since it is eliminating redundant node references and way elements. This data reduction results in faster file parsing as well as improved rendering performance since now to render a single stretch of highway one only needs to render a single linestring object as opposed to multiple linestring objects.


