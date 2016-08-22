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

Instead of getting contiguous roads

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

The algorithm utilizes a multi-hash-map to bundle together OSM way elements which share a common starting or terminating node.
This multi hash map has nodeID's as keys and WayChunk's as the values. The idea is that at any given instant, this map will contain the starting and ending point of the ways which have been evaluated till now, as keys and the corresponding way chunk as the value pointed to by these keys. Now whenever we encounter a new way, and if its starting or ending node matches with any of the existing way chunks as well as the type i.e. GeoDataVisualCategory of the way matches with the type of the way chunk, then this way is added to the way chunk and the values of the map are adjusted so that the map's keys are the starting or ending nodes of some way chunk and not the intermediary ones. This way, eventually, we are able to concat the multiple small OSM chunks of highways, railroads into singular way elements.

The reason we are using multi-hash-maps instead of regular hash maps is that at a particular node, two or more highways(linestrings) of different types may emanate or terminate. Hence a given node may be associated with two or more way chunks having different type(GeoDataVisualCategory). 

The algorithm in more detail is described below:

'''
*Concat(input_osm_file):*

* Iterate over all of the way elements having key=value tags specified during input
  * Check if the first or the last node ID of the way is present in the multi-hash-map.
  * If neither of the IDs are present
    * CreateWayChunk(way)
  * If only the first ID is present in the map
    * Check if any chunk exsits in the map which has the key as that of first ID and GeoDataVisualCategory as that of the way.
    * If such a chunk exists
      * Append the way to this chunk accordingly(reverse the way if required)
      * Delete the first ID from the map
      * Insert a new entry into the multi map having the key as last node ID of the way and value as that of the found chunk
    * If not 
      *  CreateWayChunk(way)
  * If only the last ID is present in the map
    * Check if any chunk exsits in the map which has the key as that of last ID and GeoDataVisualCategory as that of the way.
    * If such a chunk exists
      * Append the way to this chunk accordingly(reverse the way if required)
      * Delete the last ID from the map
      * Insert a new entry into the multi map having the key as first node ID of the way and value as that of the found chunk
    * If not
      * CreateWayChunk(way)
  * If both the IDs are present in the map
    * Check if any chunk exsits in the map which has the key as that of first ID and GeoDataVisualCategory as that of the way.
    * Check if any chunk exsits in the map which has the key as that of last ID and GeoDataVisualCategory as that of the way.
    * If both the chunks exist
      * Append the way and the second chunk to the first chunk and modify the map accordingly
    * If only first chunk exists
      * Append the way to the first chunk and modify the map accordingly
    * If only last chunk exists
      * Append the way to this last chunk and modify the map accordingly
    * If none of the chunks exist
      * CreateWayChunk(way)
* Finally iterate over all the WayChunks and merge the ways in each of their lists to form one single osm way for each chunk

*CreateWayChunk(way):*

* Create a new WayChunk
* Append the way to this new way chunk
* Set the GeoDataVisualCategory of the chunk as that of the way
* Insert two new entries in the multi map having the starting and ending node IDs of the way as keys and the created WayChunk as the  value.
'''

* Results *

The first image having discontinous highways represents the raw input OSM file. This file has 2704 ways in total and has a size of 4.7 MB

The way-concatenator reduced the number of ways to 812 and the size to 2.9 MB. The second image having contiguous roads represent the output of the way concatenator.

If we remove the redundant nodes from the above output using the node reducing module described in the previous post, we get a resulting file having a size of 2.5MB. This node reducer removes 15146 redundant nodes (keeping the resoltion of level 11).

If you suspect that due to node reduction there will loss in quality of rendering, then look at the below rendered image.

![road_joined]({{ site.url }}/assets/ak3.png)

The node reducer and the way concatenator have resulted in a size reduction of approx 46% for the above sample without any signifcant loss in rendering quality.



