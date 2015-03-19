---
layout: post
title: Fixing Luit's `Segmentation fault` error with GBK encoding.
date: '2015-03-19T10:38:00.001-08:00'
comments: true
author: Dat Le
tags:
modified_time: 2015-02-07T10:38:00.001-08:00'
---

Been trying to decode some Chinese datasets as a part of my work lately, I came accorss this problem.
Normally everything works just fine on my Mac OS X 10.10.2:

{% highlight bash %}
$ luit -V
luit - 1.1.1
$ echo ä¯ÀÀÁ¿ | luit -encoding gbk -x -c
盲炉脌脌脕驴
{% endhighlight %}

But on my Ubuntu 14.04 system, it wasn't that easy:

{% highlight bash %}
$ sudo apt-get install x11-utils #installing luit on Ubuntu
$ luit -V
luit - 1.1.1
$ echo ä¯ÀÀÁ¿ | luit -encoding gbk -x -c
Segmentation fault (core dumped) <--- what!?
{% endhighlight %}

Tried to search for a single working solution for a few days without any luck, so I did the necessity:

{% highlight bash %}
$ sudo apt-get install -y wget build-essential
$ wget http://invisible-island.net/datafiles/release/luit.tar.gz
$ tar -xvf luit.tar.gz
$ cd luit-20141204; ./configure; make; sudo make install
{% endhighlight %}

Apparently they fixed that in `luit 2.0` - voilà:

{% highlight bash %}
$ luit -V
luit - 2.0-20141204
$ echo ä¯ÀÀÁ¿ | luit -encoding gbk -x -c
盲炉脌脌脕驴
{% endhighlight %}

Hopefully this will show up on google whenever someone else encountered this error :).
