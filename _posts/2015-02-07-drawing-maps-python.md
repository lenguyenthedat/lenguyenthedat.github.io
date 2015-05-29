---
layout: post
title: Drawing country geological map with Python.
date: '2015-02-07T10:38:00.001-08:00'
comments: true
author: Dat Le
tags: fun, code, tutorial, python, map, visualization
modified_time: 2015-02-07T10:38:00.001-08:00'
---

Being someone who never worked with map / geo data before, it's quite a challenge for me at first. My objective was to play around with maps, its coordinate system, and maybe to apply some clustering / prediction model on top of it later on, producing some useful insights.

Anyawy, to start, I want to be able to draw an interactive and beautiful map. Below are the steps I've taken:

- **Preparation: download geojson map data and install a few Python libraries**

Map data can be downloaded at: [*mapzen/metro-extracts*](https://mapzen.com/metro-extracts/)

**Note**: For this tutorial, make sure you download the map in `imposm-geojson` format.
{: .notice} 

{% highlight bash %}
pip install geopandas
pip install git+git://github.com/jwass/mplleaflet.git
pip install git+git://github.com/mpld3/mplexporter.git
{% endhighlight %}

- **Bring it all together with Python**

Import libraries and read Singapore map data:

{% highlight python %}
import geopandas as gpd
import mplleaflet

roads = gpd.GeoDataFrame.from_file('singapore-roads.geojson')
buildings = gpd.GeoDataFrame.from_file('singapore-buildings.geojson') 
{% endhighlight %}

At this stage, you can already plot it:
{% highlight python %}
roads.geometry.plot()
{% endhighlight %}

![image](http://i.imgur.com/iKVfP8q.png)

However, the map should only contain Singapore data, you can implement its boundary with the `admin.geojson` file:
{% highlight python %}
admin = gpd.GeoDataFrame.from_file('singapore-admin.geojson')
singapore = admin.ix[0]
gpd.GeoSeries(singapore.geometry).plot()

roads = roads[roads.geometry.within(singapore.geometry)]
buildings = buildings[buildings.geometry.within(singapore.geometry)]
{% endhighlight %}

![image](http://i.imgur.com/bqcO7M8.png)

Our map now contain only Singapore data:

{% highlight python %}
roads.geometry.plot()
{% endhighlight %}

![image](http://i.imgur.com/PyrK5d3.png)

However, the map still doesn't look good nor interactive yet. We're going to solve it with `mplleaflet`:
{% highlight python %}
ax = roads.geometry.plot()
mplleaflet.display(fig=ax.figure) # To display it right at the notebook.
mplleaflet.show(fig=ax.figure) # To output _map.html file and display it in your browser.
{% endhighlight %}

- **End result**

![image](http://i.imgur.com/0nR85Ln.png)

**Note**: You can also then view it live at: [*singapore-roads.html*](http://lenguyenthedat.com/extras/singapore-roads.html)
{: .notice} 