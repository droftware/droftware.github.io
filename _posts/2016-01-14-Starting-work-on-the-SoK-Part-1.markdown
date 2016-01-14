---
layout: post
title:  "Starting work on the SoK - Blog post 1"
date:   2016-01-14 10:18:00
categories: sok open-source kde
---

![KDE SoK]({{ site.url }}/assets/kde_logo.svg)

I got to know about KDE SoK through a facebook post and straight away I hopped onto the ideas page. Since I was a complete beginner in the open source domain I was hoping to find a project with code that is easy to understand and comprehend, has good documentation for newbie programmers and whose mentors are supportive and explain even the trivial matters. 

![KDE SoK]({{ site.url }}/assets/gcompris_logo.png)

I came upon GCompris which is an educational software suite consisting of several activities and whose target user base is in the age group of 2 to 10 years. My task would involve adding new activities and/or porting existing activities from its GTK+ version to Qt Quick version.

I downloaded the code and played some of the activities. I joined their IRC channel and introduced myself to the project mentors and expressed my desire to participate in Season of KDE. The mentors Bruno Coudoin, and Johnny Jazeix were helpful and prompt in their replies. They told me that in order to contribute to GCompris I should know QML which is used to describe the user interface and Javascript for defining the logic. 
Now the catch was that I had no clue about QML. However I was determined to get selected since I had tried to contribute in some KDE organization a few months back but due to enormity and complexity of the code I had quit that time even before putting any efforts. The good thing about GCompris was their compartmentalized architecture in which all the 100+ activities were written entirely in QML and JS and were united together by a C++,Qt core which managed core functionalities. What also appealed me was that they had a new contributors page which had detailed instructions regarding how to setup git, create a new activity and sending pull requests. Since I was a newbie and had never worked with an open source organization ,an easy to follow architecture and a detailed beginners contribution page were very important.
Thus I made my decision to learn QML and apply in Season of KDE under GCompris. I started searching for some QML tutorials and got to know about the open source online book Qt5 Cadaques. Eccentric name but gem of a book ! With the help of this book I managed to build a working prototype of a Graph Coloring activity. 

I would like to point out that GCompris has a script which generates all the boilerplate for the new activity which is required for integrating the new activity with the rest of the application as well as maintaining a unified look and feel. Misquoting Niel Armstring - this shell script might be small step for the mentors but it provided a big jump for me since now I did not have to fiddle with Cmake and other build related tools and could just concentrate on the main task of creating the activity, making the coding process much simpler.
