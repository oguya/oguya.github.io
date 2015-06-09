---
layout: post
title:  "Clear RAM Memory Cache, Buffer & Swap Space"
date:   2015-06-09 06:06
excerpt: "GNU Linux has implemented efficient memory management algorithms & tools which gives you the power & flexibility to control both the physical & virtual memory on your system."
category: Linux
tags: memory,cache,swap
---

GNU Linux has implemented efficient memory management algorithms & tools which gives you the power & flexibility to control both the physical & virtual memory on your system.


### Clear Cache

There are 3 options to clear cache without interrupting any processes or services:

**Clear PageCache**
{% highlight bash %}
echo 1 > /proc/sys/vm/drop_cache
{% endhighlight %}

**Clear [dentries](http://unix.stackexchange.com/a/4403) and [inodes](http://unix.stackexchange.com/a/4403)**
{% highlight bash %}
echo 2 > /proc/sys/vm/drop_cache
{% endhighlight %}

**Clear PageCache, dentries and inodes**
{% highlight bash %}
echo 3 > /proc/sys/vm/drop_cache
{% endhighlight %}


### Clear Swap Space

Clearing swap space is as easy as disabling & re-enabling the devices/files used for swaping.
{% highlight bash %}
swapoff -a && swapon -a
{% endhighlight %}
