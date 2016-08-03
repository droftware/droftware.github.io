---
layout: post
title:  "The Why part - medium and low tile levels in Marble"
date:   2016-08-3 17:00:00
categories: gsoc open-source kde marble osgeo osm
tags: gsoc open-source kde marble
---

It has been quite some time since I have written anything about my GSoC project. Anyways, here it is. 

So what exactly is my project and what I am supposed to do?
Let’s start with my project title

*“Support for medium and low tile levels in the OSM Vector Map of Marble”*

### Background

To the uninitiated it sounds quite cryptic so let me break it down and explain it a little better. 
Marble, which is our awesome virtual globe, can display both raster as well as vector maps. Raster maps are made up several images(JPG or some other raster format) arranged in a grid like manner. Vector maps on the other hand are made up of chunks of vector data(XML, JSON etc)., each chunk having geographic information for a particular bounded area.

Depending on the zoom level, Marble loads in geographic data(raster or vector). As we zoom in further and further, the resolution of the geographic information being displayed/rendered increases. Now Marble just can't load the entire Earth’s data for a particular zoom level since beyond a certain resolution this data set goes into the range of tens and then hundreds of gigabytes. Marble resolves this problem via a geo-data storage/indexing strategy known as QuadTiles. In this method, depending on the zoom level, we divide the entire world into tiles. As the zoom level increases, the number of tiles as well as the resolution of these tiles increases. The advantage is that , when viewing a specific region of earth at a particular zoom level, only a few tiles are required as opposed to loading the entire dataset into memory.
OSM Vector Map is a a new Marble map theme which is still being worked upon, and the cool thing about this theme is that it utilizes openstreetsmap vector data to actively render and display the maps resulting in more crisp, dynamically styled maps.

### My part

Coming back to my project, I , along with my mentors Dennis Nienhüser and Torsten Rahn and fellow summer of code student David, am supposed to add support for lower and medium zoom levels to this OSM Vector map theme. Before the project started, the map supported high zoom levels and the expectation is that by the end of the project, Vector map theme will fully support lower and medium zoom levels.

Support is a pretty vague term so let me be a little more specific. Till now Marble was able to render/display only the higher zoom levels of OSM Vector map theme. For being able to properly render the lower and medium zoom levels quite a many things need to be done which one does not need with the higher zoom levels.
A question which might be bothering and which even bothered me was that if Marble was able to render higher zoom levels then why don’t we render medium and lower levels on similar principles ?

It is because of the level of detail with which everything is described in the openstreetmap data. For example osm data captures all kinds of roads, highways, bilanes, their nooks and corners; very detailed shapes for buildings. Right now, the higher levels i.e the street levels of VectorOSM directly use OSM data, which is filtered to exclude many elements. We cannot directly use OSM data for lower and medium levels and it is because of the following 2 reason.

* ### Visual 

For the lower and medium zoom levels, which you can compare to a bird’s eye view, we don’t need such detailed and precise data. At that level things are quite different. One does not observe some smaller streets and bi-lanes; multiple lane highways appear as a singular strip; nearby railway lines appeared merged, blocks of buildings appeared joined together, many low level details like poles, trees etc are just not visible. Also, since OSM data is street level data, if directly loaded it will lead to severe screen clutter. Hence we need to pre-process data so as to concatenate, cluster and merge the data elements to make them visually apt for a particular level.

![roads]({{ site.url }}/assets/ak1.png)

> When OSM data directly loaded and observed from level 11;
> This happens because a single highway can consist of several tiny OSM Way elements. Marble omits OSM elements 
> which are less then 2px on the screen for performance reasons. 


![buildings]({{ site.url }}/assets/buildings.png)

> Buildings can be clustered and then merged so as to form continous blocks

* ### Performance

As told earlier, the VectorOSM map theme directly renders the data and does not depend on pre-rendered content. Now if we directly feed in the OSM data there will be several performance issues since depending on the region being viewed, it may involve rendering of thousands or even millions of OSM elements. Hence we need to reduce the redundancy in OSM data by eliminating nodes as well as concatenating ways wherever possible.

![clear]({{ site.url }}/assets/karlsruhe_clear.png)

![cluttered]({{ site.url }}/assets/karlsruhe_cluttered.png)

> Each OSM node is a small square. See the square overlappings as redundancies which need to be removed

For doing this we are constructing an OSM data pre-processor which concatenates, eliminates, merges, does clustering so as to make OSM data suitable for live rendering for some particular level of Marble.

Also, it was decided that for the lower levels we will use Natural Earth data instead of the regular openstreetdata. It is because Natural Earth contains data which is already filtered and categorized and is available in three resolutions 110m, 50m and 10m which we can easily adapt and style to the needs of Marble. However doing this requires two major things

* ### Conversion from Shapefile to OSM format

Natural Earth vector data is present in SHP(Shapefile) format. However our Vector OSM theme requires data in OSM format. Hence we need to convert the data from SHP to OSM. There are existing tools which do this conversion but these tools lack in geometry support as well as the exact mapping of Natural Earth to OSM data. These tools need to be extended so that they are able to properly interpret Natural Earth data as well as map them to the OSM format.

* ### Styling

Stylings for various Natural Earth geographical features need to be added to Marble, some of which have an OSM equivalent like administrative boundaries and some which don’t have any OSM equivalent like Bathymetries. For doing this some Marble specific OSM tags have to be introduced. Also, the stylings must be in sync with the existing OSM stylings.

![globe]({{ site.url }}/assets/earthne.png) 

> Administrative boundaries, bathymetries, IDL , land polygon stylings were added


In this post, I have mostly described the **Why** part of my project. Over the next few posts I will try to write about the **How**  aspect i.e.  how I achieved a particular goal and why did I do it using that particular method.



