---
layout: post
title:  "The How part (1) - Converting Natural Earth SHP to OSM format"
date:   2016-08-3 21:00:00
categories: gsoc open-source kde marble osgeo osm
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

So my first GSoC task was finding efficient mechanisms/procedures for converting Natural Earth data in SHP format to OSM format. My mentors gave me two options, either create a new tool from scratch using Marble libraries on the lines of the existing shp2pn2 converter or modify the existing polyshp2osm tool. On IRC, it was discussed that if polyshp2osm.py script does not offer any advantage in extracting the metadata /semantics from SHP to OSM format resulting in suitable rendering of lakes and roads, then a new tool must be built.

## Prep Work

After experimenting with the polyshp2osm tool, I figured out the following things about it.

polyshp2osm is built upon the GDAL/OGR python bindings. GDAL/OGR is a library for reading/writing raster and vector geospatial formats. It presents a single abstract data model, for all the formats, to the calling application. polyshp2osm reads the SHP file into this abstract data model and then loops over all the possible geometries to figure out the nodes, ways and relations of the corresponding OSM file. In this regard it is quite similar to the shp2pn2 tool which reads the SHP file into a GeoDataDocument.
However, polyshp2osm is limited in the sense that it provides support only for polygons and not for linestrings and other geometries. In fact, it throws an error if we try to convert SHP files containing linestrings like ne_10m_roads and ne_10m_rivers_lake_centerlines. Thus polyshp2osm lags in geometry conversion and effort must be put in order to perform this conversion properly.

The other area of polyshp2osm is its metadata extraction and tag-mapping. The GDAL/OGR library, on which the converter is built provides direct methods for extracting the metadata for a particular geometry present in SHP file. This metadata is available in the form of key-value pairs. These key-value pairs obtained from the SHP files can be directly mapped to corresponding OSM key-value pairs. However all such mapping from the Natural Earth metadata to the OSM key-value pair has to be put into the polyshp2osm program manually since the polyshp2osm was built keeping in mind the MassGIS OpenSpace dataset and has mappings corresponding to this dataset only. Though the tool(polyshp2osm) provides a framework for tag-mapping, still effort must be put in analyzing the metadata provided by different datasets of Natural Earth and finding the corresponding OSM key-value mappings.

polyshp2osm is basically a template code which needs to be extended so that all types of geometries can be converted to OSM. Also, in-spite of having a mechanism for tag-mapping from SHP metadata to OSM key-value pair, it has no inbuilt mapping for NaturalEarth SHP data to OSM key-value and hence this mapping has to be added in.

However, eventually I settled on tweaking polyshp2osm instead of building a new converter from scratch on the lines of shp2pn2. Building a new converter will involve adding support for creating nodes, ways and relations on the existing shp2pn2 procedures which will take more time as compared to adding support for 1-2 more geometries in polyshp2osm. Also I will have to build a metadata extraction and tag-mapping feature for the new converter which are already present in the polyshp2osm tool.

#### Tweaking and fixing polyshp2osm so as to make it compatible with Natural Earth data and Marble stylings

So basically there were three types of additions/modifications which I had to do

* ### Adding support for all the geometries

As mentioned before, polyshp2osm supported conversion of only those geographic features which have a polygon type of geometry.  However Natural Earth data consisted of all forms of geometries like linestrings, multilinestrings, multipolygons and points (all these geometries are defined by the SHP format). Apart from this, the entire code was written in a script like manner and was not very modular. So before adding any support for remaining geometries I refactored the code to make it a little modular(although as of now it is still not pythonic and is not OOP based) so that code components related to extracting metadata; mapping to OSM key-value pairs; writing  OSM node, way , relations elements can be reused for different geometries. After this refactoring, the task of adding additional SHP geometries became straightforward.

* ### Mapping metadata to OSM key-value pairs

This task involved mapping Natural Earth metadata to the corresponding OSM key-value pairs so that Marble is able to render all the features in the resulting OSM file appropriately. I had to write a dictionary, consisting of around 120 entries which contained all the corresponding metadata to key-value mappings. Using this dictionary the program inserts appropriate key-value tags in the resulting node, way or relation.

* ### Fixes

Problems started to emerge when the converted files were used to make vector tiles for Marble. In the initial version of the tiles few of the geographical features such as land masses were not visible. It was eventually found that the converter is not outputting in the correct format. According to http://wiki.openstreetmap.org/wiki/OSM_XML, in an OSM file there should be a block of nodes then a block of ways finally followed by a block of relaitions. The tool was outputting nodes, ways and relations in a random order.

After fixing this, everything was getting rendered suitably however osmconvert, which is used to cut the OSM file into tiles continued to show warnings, sequence errors. This was because osmconvert expects the elements in the OSM file to be in non-decreasing ID's.

With these two fixes in place, things have been fine till now.

So this was all about shp to osm conversion. In the next post I will try to explain about the various style related changes and the additional geographical features which I have added in Marble.
