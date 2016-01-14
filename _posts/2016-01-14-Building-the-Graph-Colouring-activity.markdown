---
layout: post
title:  "Building the Graph Coloring activity: Part 2"
date:   2016-01-14 18:29:00
categories: sok open-source kde
---
![Gcompris]({{ site.url }}/assets/gcompris_logo.png)

The Graph Coloring activity is based on the famous NP Complete problem in which a graph ‘s nodes have to be colored in such a manner such that no two adjacent nodes have the same color.

My main aim for creating this activity was to prove the mentors that I can work in QML. I was able to code the working demo in QML but this code of mine was poorly built. My design was not extensible since I had not used JSON datasets for adding levels but had only hardcoded the graph information. I had copy pasted the code instead of using a model-views and repeaters. Also I had not kept the positions and sizes of elements relative to the screen ,which is very important for GCompris since it’s a cross platform application and is used on desktops, tablets and smartphones alike.

![Gcompris]({{ site.url }}/assets/graph_coloring_icon.svg)

Days passed by and finally the mail came with the good news of my selection. Aruna Sankaranarayanan was assigned as my project mentor. However due to overload of projects and end semester exams I was not able to devote much time to GComrpis. 
After everything academic related got over I took a break and it is after this break I again started the Graph Coloring activity. This time I jotted down the points which the mentors had told me after they had reviewed the code. I read the codes of other activities in GCompris so as to understand the correct way of representing and coding things as well as to pick and reuse certain things in my own Graph Coloring activity. I assumed that reading the codes of other will be an easy thing but it actually proved otherwise. The first time I picked a code to read and understand was a challenge but things eventually got better with me gaining a little speed.
Until now, any coding related task which I had done had not been “engineered” but was “hacked” to make the ends meet. In the open source world which I experienced though GCompris I found that there was much emphasis on quality and lousy work was just not permitted. Things are there for a specific well defined reason.
I submitted my second version after my disastrous first version. I had tried to rectify most of the things like extensibility, using model-views and repeaters instead of copy pasting. I thought that now my work on the Graph Coloring activity is over and that now I can start working on a new educational activity. This time also I was proved wrong. The mentors expected a high quality of code and told me rectify and correct a few things and thus began the iterative cycle of rectification and enhancements. I had to change things like representation of graphs in qml so as to make it more suitable for the application, and drawing the edges of the graphs using rectangles instead of canvas for enhanced performance. 

![Gcompris]({{ site.url }}/assets/gcompris_s2.png)

One thing which troubled me the most was to make the size and position of nodes of the graph relative to the screen. Each time I tried making some attribute relative something or the other got affected and I had to again tweak some things. However the mentors made me realize that this thing was very important for GCompris due to its cross platform nature and hence the game must be playable on any kind of standard resolution. Eventually I was able to make the size and position relative after repeated hit and trials.

![Gcompris]({{ site.url }}/assets/gcompris_small_res.png)

Also I made the activity real time i.e. previously there was an Ok button which need to be pressed in order to get the coloring verified as to whether it is valid or not but now if you have colored the graph correctly you will automatically be notified and will be advanced to the next level. Apart from this animations were also added so as to help a child indicate in which adjacent nodes he has filed the same color.

![Gcompris]({{ site.url }}/assets/gcompris_same_color.png)

One thing which I have observed is that GCompris tries to base its activities on good pedagogic principles. There were discussions in the mailing list regarding which features to add or remove so as to make a child better understand a certain concept.
A fine issue that was raised by Bruno was regarding color blind children. He told me to include shapes/letters apart from colors so that color blind children can easily label nodes with different shapes like stars,diamonds,hearts etc instead of coloring nodes. Hence an additional option was added so as to switch the mode from color to shapes and vice-versa. Eventually the shapes version was made the default version.
![Gcompris]({{ site.url }}/assets/gcompris_shapes.png)
Finally some graphs ranging from easy to difficult were added. Bruno helped me in this task by providing a script which parsed the information from svg [graphs provided by Wikipedia][wiki-graph] into our application defined graph format.
![Gcompris]({{ site.url }}/assets/wiki_graphs.png)
Now since the activity is almost complete I am planning on starting a new activity. I have discussed some ideas with Aruna regarding a dictation activity and an algebra one. I am hoping to complete these before the end of Season of KDE.

[wiki-graph]: https://en.wikipedia.org/wiki/Gallery_of_named_graphs
