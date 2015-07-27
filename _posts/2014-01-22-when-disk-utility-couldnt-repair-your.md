---
layout: post
title: When Disk Utility couldn't repair your Hard Drives anymore
date: '2014-01-22T01:19:00.003-08:00'
author: Dat Le
tags: mac, osx, bugs, tips
modified_time: '2014-01-22T01:19:42.619-08:00'
comments: true
blogger_id: tag:blogger.com,1999:blog-3261104696526323937.post-2716440357855923636
blogger_orig_url: http://lenguyenthedat.blogspot.com/2014/01/when-disk-utility-couldnt-repair-your.html
---

![image](http://i.imgur.com/6pXKdmv.png)

{% highlight bash %}
$ diskutil list # To list all your connected HDD
$ sudo fsck_hfs -rf /dev/disk2s99 # To repair your disk2s99,
                                  # this might take a while.
                                  # **USE AS YOUR OWN RISK.**
** /dev/rdisk2s2
   Executing fsck_hfs (version hfs-285).
** Checking Journaled HFS Plus volume.
   The volume name is Time Machine at Work
** Checking extents overflow file.
** Checking catalog file.
** Rebuilding catalog B-tree.
** Rechecking volume.
** Checking Journaled HFS Plus volume.
...
{% endhighlight %}

**Note:** If you encounter `Resource busy` error, try killing your current Disk Utility's fsck_hfs process from Activity Monitor or your Terminal - 60% of the time, it works every time. 
{: .notice}