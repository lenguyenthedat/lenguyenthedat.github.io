---
layout: post
title: 'Docker cache and apt-get update.'
datate: '2015-07-13T12:45:00'
comments: true
author: Dat Le
tags: bug, linux, docker, code
modified_time: 2015-07-13T12:45:00'
---

![image](http://i.imgur.com/YyJFkvc.png)
<div></div>

If you ever run into such error below when building your Docker image...

{% highlight bash %}
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/l/linux/linux-libc-dev
_3.13.0-55.94_amd64.deb  404  Not Found [IP: 91.189.91.14 80]

E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
The command '/bin/sh -c apt-get install -y s3cmd postgresql wget build-essential unzip gawk'
returned a non-zero code: 100
{% endhighlight %}

It's likely you had these as separated lines in your Dockerfile (before `apt-get install` commands):

{% highlight bash %}
RUN apt-get update
RUN apt-get install -y s3cmd postgresql wget
{% endhighlight %}

You should instead combine them in a single line, for example:

{% highlight bash %}
RUN apt-get update && apt-get install -y s3cmd postgresql wget
{% endhighlight %}

Here is why:

- By default, Docker cache your commands to reduce time spent building images. Unless there was any change before such commands (or at the same line).
- Linux distros are updated regularly, and it requires you to run `apt-get update` frequently otherwise `apt-get install` might not works as intended.

So, if you have built the image earlier (with a linux distro that recently had some updates), and you want to add some more libraries, for example:

{% highlight bash %}
RUN apt-get update
RUN apt-get install -y s3cmd postgresql wget build-essential unzip gawk
{% endhighlight %}

The `apt-get update` command will get bypassed since it's already get cached, your package management (i.e `apt-get`) would not be up-to-date while you thought it should always be.

**Note:** 
After a few search, I found this issue on Docker repo: https://github.com/docker/docker/issues/1996 too bad it's closed because there were not many real world use cases - they might be right though. Of course, we can always use `--no-cache=true` as an option for our `docker build` command, but it will build everything all over again everytime - you probably don't want that.
{: .notice}

<div></div>

Anyway, below are some few links that I find useful for my Docker use:

- [https://docs.docker.com/articles/dockerfile_best-practices/](https://docs.docker.com/articles/dockerfile_best-practices/)
- [http://crosbymichael.com/dockerfile-best-practices.html](http://crosbymichael.com/dockerfile-best-practices.html)
- [http://crosbymichael.com/dockerfile-best-practices-take-2.html](http://crosbymichael.com/dockerfile-best-practices-take-2.html)

And that should be it.