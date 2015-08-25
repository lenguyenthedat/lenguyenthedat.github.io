---
layout: post
title: Write your resume the programmer's way
date: '2015-01-15T15:06:00.001-08:00'
comments: true
author: Dat Le
tags: fun, code, resume, tutorial
modified_time: '2015-01-15T10:16:15.429-08:00'
---

Came across this gem - [*jsonresume.org*](https://jsonresume.org/) while trying to update my resume and I decided to give it a try.
Turned out, it was very easy to setup and to use on OS X.
Below are all the steps that I needed to do:

- **Installing homebrew** - from [*brew.sh*](http://brew.sh)

{% highlight bash %}
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
{% endhighlight %}

- **Installing npm and resume-cli**

{% highlight bash %}
brew install npm
npm install -g resume-cli
{% endhighlight %}

- **Work on your resume.json**

{% highlight bash %}
# initiate your resume.json file
resume init
# once finishing
resume export --theme=paper resume.html -F
{% endhighlight %}

**Note**: You can also do this online at [*registry.jsonresume.org*](http://registry.jsonresume.org/)
{: .notice}

- **Voilà:** [*lenguyenthedat.html*](http://lenguyenthedat.com/extras/resume.html)!