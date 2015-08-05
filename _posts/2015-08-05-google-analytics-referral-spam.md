---
layout: post
title: 'Google Analytics Referral Spam.'
datate: '2015-08-05T12:45:00'
comments: true
author: Dat Le
tags: spam, google analytics, rant, technology
modified_time: 2015-08-05T12:45:00'
---

![image](http://i.imgur.com/UueG6ii.png)
<div></div>
/rant
<div></div>
If you are using or planing to use [Google Analytics](www.google.com/analytics/) for visitors tracking purpose on your website, you probably want to have another closer look into it and rethink on the platform you might want to chose in the end, especially when you have a production system and want the best insights you can achieve.
<div></div>
A simple search on ["Google Analytics referral spam"](https://www.google.com/search?q=Google+Analytics+referral+spam) keyword would give you millions of results on the web about it, on phantom traffics definition, why and how to detect / remove such traffics.
<div></div>
This post is, however, not about solving this problem. It's about me ranting and being annoyed. Below is what I see when logging into Google Analytics Dashboard earlier today:
<div></div>
![image](http://i.imgur.com/Or6vjqT.png)
<div></div>
See those phantom traffics? All you can do about this is to remove them each time when you see a new one, through the interface. The whole process is extremely manual and painful:

- [How to Stop Spam Bots from Ruining Your Analytics Referral Data](https://moz.com/blog/how-to-stop-spam-bots-from-ruining-your-analytics-referral-data)

- [Stop floating-share-buttons.com referral spam in Google Analytics](http://www.ohow.co/stop-floating-share-buttons-com-referral-spam-google-analytics/)

**Note:** 
It's even worse trying to do it retrospectively to fix your historical tracking data.
{: .notice}
<div></div>
Imho, one of the most essential characteristic of a mature tracking platform is that it should be able to eliminate spam itself effectively. Technically, it's not even a hard problem to begin with - there are thousands of ways to detect spammy traffics and to eliminate them with high confident level. In this case: a simple [Decision Tree](http://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) or [Naive Bayes](http://scikit-learn.org/stable/modules/naive_bayes.html) classifier model would simply work.
<div></div>
It's also important to note that this is a [10-years-old Google product](https://en.wikipedia.org/wiki/Google_Analytics), and with all the great engineers they have, it should not take more than a few days (maybe a week?) to fix it properly. Why didn't they fix it already? Well, I have no idea, but since I'm using it for [free](https://productforums.google.com/forum/#!topic/analytics/zOiURSrntqA), what can I say?
