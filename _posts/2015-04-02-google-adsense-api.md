---
layout: post
title: Google AdSense API with Python.
date: '2015-04-02T12:45:00'
comments: true
author: Dat Le
tags: code, tutorial, adsense, data, api, python
modified_time: 2015-04-02T12:45:00'
---

Following my last blog post on [Youtube Analytics API](http://lenguyenthedat.com/youtube-analytics-api/), this post will be about [Google AdSense (Management) API](https://developers.google.com/adsense/management/index).

<br>

**Prerequisites**: You will need python 2.7 and a few libraries:

{% highlight bash %}
$ apt-get install -y python2.7 python2.7-dev python-setuptools python-pip
$ pip install --upgrade pip httplib2 google-api-python-client googleads
{% endhighlight %}

<br>

**Authentication and Details**: Below are a few steps you need to do beforehands:

- Create new Google project at [google's developers console](https://console.developers.google.com/)
- Enable AdSense APIs at the `APIs & auth > APIs` tab:

<br>

![image](http://i.imgur.com/UWKvu1F.png)

- Generate your OAuth's `client_secret.json` credentials at the `APIs & auth > Credentials` tab:

<br>

![image](http://i.imgur.com/ENAtMLE.png)

<br>

![image](http://i.imgur.com/5eu7Mhj.png)

(Click `Download JSON` after done)

- Get your `publisher_id` at your [AdSense Home Page](https://www.google.com/adsense/app#main/home) (`publisher_id` will be displayed in the top right corner in a form of:`pub-12345678901234`):

<br>

![image](http://i.imgur.com/RhHRJ6f.png)

<br>

**Firing your first ever API call** (and also, generate your `oauth2` credential file i.e `adsense.dat`):

- In other to do this, we will use my version of [AdSense API Sample file](https://github.com/lenguyenthedat/google-data-api-tutorials/blob/master/AdSense/generate_report.py):

{% highlight bash %}
$ git clone https://github.com/lenguyenthedat/google-data-api-tutorials.git
$ cd google-data-api-tutorials # Make sure you have `client_secret.json` in google-data-api-tutorials
$ cd AdSense/
$ python generate_report.py --account_id pub-12345678901234 # you can also use --noauth_local_webserver if you run this on a remote server
{% endhighlight %}

- It's also adviseable that you have a look at [AdSense Management API Page](https://developers.google.com/adsense/management/metrics-dimensions) for the list of `Metrics`, `Dimensions` for your own reports.

- Sample result:

{% highlight bash %}
$ python generate_report.py --account_id pub-12345678901234
Data for AdSense Account ID: pub-12345678901234
MONTH                     PLATFORM_TYPE_NAME        PAGE_VIEWS                AD_REQUESTS               AD_REQUESTS_COVERAGE      CLICKS                    AD_REQUESTS_CTR          
2015-03                   Desktop                   12345                     123133                    0.1234                    1234                      0.1234                   
2015-03                   High-end mobile devices   12345                     123123                    0.1234                    1234                      0.1234                   
2015-03                   Other devices             123                       123                       0.1234                    1234                      0.1234                   
2015-03                   Tablets                   12345                     123123                    0.1234                    1234                      0.1234                     
2015-04                   Desktop                   12345                     12313                     0.1234                    1234                      0.1234                   
2015-04                   High-end mobile devices   23154                     27912                     0.1234                    1234                      0.1234                   
2015-04                   Other devices             12                        12                        0.1234                    1234                      0.1234                   
2015-04                   Tablets                   12345                     1234                      0.1234                    1234                      0.1234
{% endhighlight %}
