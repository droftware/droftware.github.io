---
layout: post
title:  "The How part (4) - Automating tile generation for lower levels"
date:   2016-08-21 18:00:00
categories: gsoc open-source marble  
tags: gsoc open-source kde marble
---

( This post is related to my GSoC project with Marble. For more more information you can read the [introductory post]({% post_url /blog/2016-08-3-why-part-gsoc %}) )

After revamping shp2osm tool and adding the missing styles, the only thing remaining for supporting lower level tiles was automating the process of tile generation for lower levels. Before the process was automated, in order to create the lower level tiles one needed to manually download the Natural Earth shapefiles, combine and convert the shapefiles into a single OSM file, using the existing vector tile creator of Marble (which divides a large OSM file into small chunks of OSM vector tiles) create the tiles.

I wrote a script which simplifies and automates the process of generating lower level tiles for Marble. The script takes a text file as input. This text file includes a listing of the zoom levels and the Natural Earth geographical features which are to be included in those zoom levels.

```
*0
ne_110m_land
ne_110m_geographic_lines
*1
ne_110m_land
ne_110m_geographic_lines
ne_110m_admin_0_boundary_lines_land
*3
ne_110m_land
ne_110m_geographic_lines
ne_110m_admin_0_boundary_lines_land
ne_110m_admin_1_states_provinces_lines
ne_110m_admin_0_pacific_groupings
ne_110m_rivers_lake_centerlines
ne_110m_lakes
ne_10m_bathymetry_K_200
ne_10m_bathymetry_G_4000
```
As you can see, the above input file describes what all Natural Earth features are to be included for 0th, 1st and 3rd levels of the vector OSM theme of Marble 

Using the above input file, the script checks the input directory for the Natural Earth geographical features specified in the file. If any geographical feature is not present, the script downloads the files in zipped form and then unzips it. Once all the required Natural Earth features are present in the input directory, using the shp2osm tool it combines all the data for a particular level and creates a single OSM file containing all the Natural Earth features specified in the input file. Then using the existing vector tile creator, it creates all the required OSM tiles , arranged properly in respective folder and sub-folders for the levels specified in the input file.

With this tool in place, we can now easily generate the lower level vector tiles for Marble. 

![level_3]({{ site.url }}/assets/lev31.png)
**level 3**

![level_5]({{ site.url }}/assets/lev51.png)
**level 5**

![level_7]({{ site.url }}/assets/lev71.png)
**level 7**

![level_9]({{ site.url }}/assets/lev91.png)
**level 9**

<iframe width="854" height="480" src="https://www.youtube.com/embed/4pEJYOS62KQ" frameborder="0" allowfullscreen></iframe>



