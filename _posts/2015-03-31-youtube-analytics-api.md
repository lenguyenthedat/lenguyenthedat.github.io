---
layout: post
title: Youtube Analytics API with Python.
date: '2015-03-31T15:45:00'
comments: true
author: Dat Le
tags:
modified_time: 2015-03-31T15:45:00'
---

Been writing some code to get data with Google APIs in the past few days, I figured it would be helpful (at least for myself!) to write a few short tutorials about them. I will start with [Youtube Analytics API](https://developers.google.com/youtube/analytics/) since it seems to be the most commonly used these days.

<br>

**Pre-requisitses**: You will need python 2.7 and a few libraries:

{% highlight bash %}
$ apt-get install -y python2.7 python2.7-dev python-setuptools python-pip
$ pip install --upgrade pip httplib2 google-api-python-client
{% endhighlight %}

<br>

**Authentication and Details**: Below are a few steps you need to do beforehands:

- Create new Google project at [google's developers console](https://console.developers.google.com/)
- Enable Youtube APIs at [your-actual-project-id's API page](https://console.developers.google.com/project/your-actual-project-id/apiui/api)
- Generate your OAuth's `client_secret.json` credentials at [your-actual-project-id's credentials page](https://console.developers.google.com/project/your-actual-project-id/apiui/credential):
![image](http://i.imgur.com/5eu7Mhj.png)

(Click `Download JSON` after done)

- Get your `content_owner_id` at your [Youtube's dashboard](https://cms.youtube.com) (`content_owner_id` will be displayed in the url - in this case: `thIsiSaSampLeContentOwnErID`):
![image](http://i.imgur.com/pcMhqic.png)

- Generate your `-oauth2.json`. In other to do this, the easiest way is to use this [Youtube API Sample file](https://github.com/youtube/api-samples/blob/master/python/yt_analytics_report.py):

{% highlight bash %}
$ cd /my-folder/
$ wget https://raw.githubusercontent.com/youtube/api-samples/master/python/yt_analytics_report.py
$ # Make sure you have `client_secret.json` in your current directory aka /my-folder/
$ python yt_analytics_report.py # you can also use --noauth_local_webserver if you run this on a remote server
{% endhighlight %}

<br>

**Firing your first ever API call**:

- Again, it's probably easiest to follow the above `yt_analytics_report.py` file. It's also adviseable that you have a look at [Google Channel Reports API Page](https://developers.google.com/youtube/analytics/v1/channel_reports) for the list of `Metrics`, `Dimensions`, and `Filters` for your own reports.

- For this example, I would modify `yt_analytics_report.py`'s `run_analytics_report()` as below, with my own `content_owner_id` and `channel_id`:

{% highlight python %}
analytics_query_response = youtube_analytics.reports().query(
  ids="contentOwner==thIsiSaSampLeContentOwnErID",
  filters="channel==thIsiSaSampLeChanNeLID",
  metrics=options.metrics,
  dimensions=options.dimensions,
  start_date=options.start_date,
  end_date=options.end_date,
  max_results=options.max_results,
  sort=options.sort
).execute()
{% endhighlight %}

- Test run:

{% highlight bash %}
$ python yt_analytics_report.py
Analytics Data for Channel thIsiSaSampLeChanNeLID
video                views                comments             favoritesAdded       favoritesRemoved     likes                dislikes             shares              
123testRW4w          230.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123testv3Lc          229.0                0.0                  0.0                  0.0                  1.0                  0.0                  0.0                 
123testGxx8          161.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123testOmcI          133.0                0.0                  0.0                  0.0                  0.0                  0.0                  0.0                 
123test5pLI          131.0                0.0                  0.0                  0.0                  6.0                  0.0                  0.0                 
123testbswQ          118.0                0.0                  0.0                  0.0                  0.0                  0.0                  1.0     
{% endhighlight %}
