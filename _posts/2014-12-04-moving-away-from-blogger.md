---
layout: post
title: Moving away from blogger
date: '2014-12-04T05:06:00.001-08:00'
author: Dat Le
tags:
modified_time: '2014-11-05T10:16:15.429-08:00'
---

![image](http://i.imgur.com/IZyNfOy.png)

<br>
<br>

That's it, I've had enough. Blogging with [Blogger](https://www.blogger.com) is simply inefficient and annoying as hell.
I'm glad I made this decision. Below are the steps that I've taken:

<br>

**Step 1: setup your own github.io page**

Simply create a new repository named `your_github_id.github.io`, instructions [here](https://pages.github.com/). 

To get your own domain, register one and follow these [steps](https://help.github.com/articles/setting-up-a-custom-domain-with-github-pages/) to configure your CNAME dns.

<br>

**Step 2 (optional): migration**

Export your blogger posts, instructions [here](https://support.google.com/blogger/answer/97416). 

Import it with [jekyll import for blogger](http://import.jekyllrb.com/docs/blogger/).

Now you should have a "_posts" folder containing all your jekyll-ready posts.

<br>

**Step 3: chose a theme**

They are all [here](http://jekyllthemes.org/).

So far, [hyde](https://github.com/poole/hyde) and [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes) work best for me.

Simply clone any of them and copy all the files and folders over to your github.io repository. Follow the instructions from the theme you chose.

<br>

**Step 4: install jekyll and start blogging**

Instructions [here](https://help.github.com/articles/using-jekyll-with-pages/).

- Install Ruby.
- Install Bundler: `gem install bundler`.
- Install Jekyll: `bundle install`.

Build and serve the pages internally: `bundle exec jekyll serve`.

