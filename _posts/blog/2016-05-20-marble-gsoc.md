---
layout: post
title:  "Google Summer of Code with Marble !"
date:   2016-05-20 10:18:00
categories: gsoc open-source kde marble osgeo osm
tags: gsoc open-source kde marble
---

Hello everybody,
I am Akshat Tandon and I am a computer science undergraduate at 
IIIT­-H, India.
I have been selected in GSoC and this summer I will be working on Marble to improve its vector tiling and rendering capabilities.

![gsoc logo]({{ site.url }}/assets/marble-logo-2.png)

*Marble, part of KDE software suite, is an open source virtual globe with tons of awesome features.*

### Current state of things

As of now Marble supports rendering of OpenStreetMap vector data for 
higher tile levels. In order to make the vector OSM map theme complete and 
end ­user ready, support for medium and lower tile levels must be added.

### What needs to be done

For supporting low level rendering( levels 1, 3, 5, 7, 9), Marble should 
render using the Natural Earth vector dataset instead of the OSM vector 
dataset. The reason for this is that Natural Earth dataset has categorically 
arranged and filtered data which is required for step by step rendering of 
lower levels. Apart from this the rendered data should be styled so as to 
match the OSM theme and tiling support must be added on the server and 
client side.  
 
For the medium level tiles, rendering must be performed using the OSM 
dataset. OSM dataset gives us many small chunks and nodes for a 
particular geographic data item. These chunks and nodes need to be 
filtered and reduced to a manageable size for each specific level so as to 
get optimal performance and less screen clutter. 
 
Rendering of higher level tiles need to be improved by adding support for 
roof­types and texture tags of buildings as well as by adding support for 
street markers.

### My part of work

My work will majorly involve adding vector tile support at low levels using the Natural Earth dataset.
This dataset is provided in SHP format and hence prior to any coding, the vector data in SHP format will need to be
converted into OSM format using pre-existing tools such as shp2osm. After this we will have the OSM files but they are not yet tiled. These files would contain an enormous amount of data and if we load all this data into Marble then it would go out of memory and any color or styling would not exist.

![Marble]({{ site.url }}/assets/marble_sc1.png)
*Natural Earth road dataset loaded in Marble. Notice the amount of detail, leading to screen clutter and the lack of any styling.*

After this conversion, my actual work starts in which I have to build a tool using the Mable libraries.

1. Which loads all these OSM files containg Natural Earth data.
2. Merges all the data in a single document.
3. Simplifies the data for a certain zoom level by reducing and filtering the number of nodes required for rendering.
4. Concatenates the polygons and polyline where it makes sense.
5. Splits the data into lots of different rectangular tiles.	

The core of the work will involve lots of mathematical problems (e.g. simplifying paths, concatenating paths, averaging paths, clipping paths to a rect, drawing coastlines etc.)

The goal is that by the end of GSoC, we will have a tool which cuts the natural earth dataset into tiles in 
OSM file format which are properly styled and rendered by Marble for each tile level in the same way as they are
drawn for the bitmap tiles on openstreetmap.org


