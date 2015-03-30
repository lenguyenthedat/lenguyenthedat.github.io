---
layout: post
title: Minimal Tmux Cheatsheet.
date: '2015-03-30T10:38:00.001-08:00'
comments: true
author: Dat Le
tags:
modified_time: 2015-03-30T10:38:00.001-08:00'
---

It's important to use `tmux` to run time-consuming scripts on remote servers, especially when they are critical and/or my internet connection is not very reliable - at least, that's my main reason for using tmux. Below is a minimal cheat sheet for my own usage.

- Create new session
{% highlight bash %}
$ tmux new -s session_name
{% endhighlight %}

- Logout of current session:

`Ctrl + b; d`

- Terminate or kill current session:

`Ctrl + d`

- Enable scrolling / navigating in current session:

`Ctrl + b; [`

- Navigating:

`Ctrl + Page Up / Page Down / Up / Down` and `q` to quit scrolling session

- Terminate or kill a session:
{% highlight bash %}
$ tmux kill-session -t session_name
{% endhighlight %}

- List all active sessions:
{% highlight bash %}
$ tmux ls
{% endhighlight %}

- Attach a session:
{% highlight bash %}
$ tmux attach -t session_name
{% endhighlight %}

Happy `tmux`-ing!