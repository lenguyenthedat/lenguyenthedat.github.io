---
layout: post
title: Using Gource to visualize multiple git repositories at once
date: '2014-12-05T05:06:00.001-08:00'
comments: true
author: Dat Le
tags:
modified_time: '2014-11-05T10:16:15.429-08:00'
---

<iframe src="//player.vimeo.com/video/113024805" width="500" height="281" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe> 

<p><a href="http://vimeo.com/113024805">Dat at Zalora</a> from <a href="http://vimeo.com/user21916034">Dat Le</a> on <a href="https://vimeo.com">Vimeo</a>.</p> <br> <br>

Below are the steps to generate the above clip.

Reference: [Gource](https://code.google.com/p/gource/) and [GourceMashups](https://code.google.com/p/gource/wiki/GourceMashups)

- Generate gource log file for each of the repository

{% highlight bash %}
gource --output-custom-log repo1.txt repo1
gource --output-custom-log repo2.txt repo2
...
{% endhighlight %}


- Using repo name sparately for each of them:

{% highlight bash %}
sed -i -r "s#(.+)\|#\1|/repo1#" repo1.txt
sed -i -r "s#(.+)\|#\1|/repo2#" repo2.txt
...
{% endhighlight %}

- Note: the above code doesn't work if you use OS X, use this instead:

{% highlight bash %}
sed "s/\|\//\|\/copytables\//" repo1.txt > repo1.tmp.txt; mv repo1.tmp.txt repo1.txt
sed "s/\|\//\|\/copytables\//" repo1.txt > repo2.tmp.txt; mv repo2.tmp.txt repo2.txt
...
{% endhighlight %}

- Squash them together:

{% highlight bash %}
cat *.txt | sort -n > combined.txt
{% endhighlight %}

- Generate movie file:

{% highlight bash %}
gource combined.txt --key --title "Your clip name" --bloom-multiplier 0.5 --bloom-intensity 0.5 \
--user-image-dir /Users/datle/Zalora/avatar/ --hide filenames --seconds-per-day 0.2 \
--auto-skip-seconds 1 -1280x720 -o - \
| ffmpeg -y -r 60 -f image2pipe -vcodec ppm -i - -vcodec libx264 -preset ultrafast -pix_fmt yuv420p -crf 1 -threads 4 -bf 0 combined.mp4
{% endhighlight %}

PS: it has been fun :)
