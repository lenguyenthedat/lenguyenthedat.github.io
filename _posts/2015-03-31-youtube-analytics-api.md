---
layout: post
title: Youtube Analytics API with Python.
date: '2015-03-31T15:45:00'
comments: true
author: Dat Le
tags:
modified_time: 2015-03-31T15:45:00'
---

In the past few days, I've been working on retrieving data from various Google Products with their APIs, I figured it would be helpful (at least for myself!) to write a few short tutorials about them.

For a start, I would do it for [Youtube Analytics API](https://developers.google.com/youtube/analytics/) since it seems to be commonly used these days.

<br>

**Prerequisites**: You will need python 2.7 and a few libraries:

{% highlight bash %}
$ apt-get install -y python2.7 python2.7-dev python-setuptools python-pip
$ pip install --upgrade pip httplib2 google-api-python-client
{% endhighlight %}

<br>

**Authentication and Details**: Below are a few steps you need to do beforehands:

- Create new Google project at [google's developers console](https://console.developers.google.com/)
- Enable Youtube APIs at the `APIs & auth > APIs` tab:

<br>

![image](http://i.imgur.com/zjPAhSs.png)

- Generate your OAuth's `client_secret.json` credentials at the `APIs & auth > Credentials` tab:

<br>

![image](http://i.imgur.com/ENAtMLE.png)

<br>

![image](http://i.imgur.com/5eu7Mhj.png)

(Click `Download JSON` after done)

- Get your `content_owner_id` and `channel_id` of choice at your [Youtube's dashboard](https://cms.youtube.com) (`content_owner_id` will be displayed in the url - in this case: `thIsiSaSampLeContentOwnErID`):

<br>

![image](http://i.imgur.com/wB1uSoB.png)

<br>

**Firing your first ever API call** (and also, generate your `-oauth2.json` file):

- In other to do this, we will use my version of [Youtube API Sample file](https://github.com/lenguyenthedat/google-data-api-tutorials/blob/master/Youtube/yt_analytics_report.py):

{% highlight bash %}
$ git clone https://github.com/lenguyenthedat/google-data-api-tutorials.git
$ cd google-data-api-tutorials # Make sure you have `client_secret.json` in google-data-api-tutorials
$ cd Youtube/
$ python yt_analytics_report.py  --channel_id thIsiSaSampLeChanNeLID --content_owner_id thIsiSaSampLeContentOwnErID # you can also use --noauth_local_webserver if you run this on a remote server
{% endhighlight %}

- It's also adviseable that you have a look at [Google Channel Reports API Page](https://developers.google.com/youtube/analytics/v1/channel_reports) for the list of `Metrics`, `Dimensions`, and `Filters` for your own reports.

- Sample result:

{% highlight bash %}
$ python yt_analytics_report.py  --channel_id thIsiSaSampLeChanNeLID --content_owner_id thIsiSaSampLeContentOwnErID
Analytics Data for channel: thIsiSaSampLeChanNeLID
video                views                comments             favoritesAdded       favoritesRemoved     likes                dislikes             shares              
123testRW4w          230.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123testv3Lc          229.0                0.0                  0.0                  0.0                  1.0                  0.0                  0.0                 
123testGxx8          161.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123testOmcI          133.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123test5pLI          131.0                0.0                  0.0                  0.0                  6.0                  0.0                  0.0                 
123testbswQ          118.0                0.0                  0.0                  0.0                  0.0                  0.0                  1.0     
{% endhighlight %}
