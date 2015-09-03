---
layout: post
title:  "Mounting Partitions Using Systemd"
date:   2015-09-01 18:06
excerpt: "
Recently, I discovered you can mount partitions using systemd.mount by writing a mount unit file. In this blog post, we'll talk about systemd.mount & how you can use it to mount partitions."
category: Linux
tags: systemd
---

[Systemd](http://www.freedesktop.org/wiki/Software/systemd) is gradually becoming the de facto init system & service manager replacing the old sysV init scripts & upstart. Recently, I discovered you can mount partitions using [systemd.mount](http://www.freedesktop.org/software/systemd/man/systemd.mount.html) by writing your own `.mount` [systemd unit file](http://www.freedesktop.org/software/systemd/man/systemd.unit.html).


![super suprised]({{ site.url }}/assets/2015-09-01-systemd-mount-partition/suprised-cat.jpg)


After _RTFM'ing_, I realized, under the hood, systemd just runs [mount command](http://linux.die.net/man/8/mount) to mount the specified partition with the specified mount options listed in the mount unit file. Basically, you need to specify the following options in your unit file:

- `What=` a partition name, path or UUID to mount
- `Where=` an absolute path of a directory i.e. path to a mount point. If the mount point is non-existent, it will be created
- `Type=` file system type. In most cases [mount command](http://linux.die.net/man/8/mount) auto-detects the file system
- `Options=` Mount options to use when mounting

In the end, you can convert your typical fstab entry such as this:
{% highlight bash %}
UUID=86fef3b2-bdc9-47fa-bbb1-4e528a89d222 /mnt/backups    ext4    defaults      0 0
{% endhighlight sh%}

to:
{% highlight ini %}
[Mount]
What=/dev/disk/by-uuid/86fef3b2-bdc9-47fa-bbb1-4e528a89d222
Where=/mnt/backups
Type=ext4
Options=defaults
{% endhighlight %}


![I Got This!]({{site.url}}/assets/2015-09-01-systemd-mount-partition/i-got-this.gif)


So I wrote a simple systemd mount unit file — `/etc/systemd/system/mnt-backups.mount` — which didn't work at first because I fell victim to one of the `systemd.mount` pitfalls:

> Mount units must be named after the mount point directories they control. Example: the mount point /home/lennart must be configured in a unit file home-lennart.mount.

Huh? Yes that's right! The unit filename should match the mount point path.

`mnt-backups.mount` mount unit file:

{% highlight ini %}
[Unit]
Description=Mount System Backups Directory

[Mount]
What=/dev/disk/by-uuid/86fef3b2-bdc9-47fa-bbb1-4e528a89d222
Where=/mnt/backups
Type=ext4
Options=defaults
{% endhighlight %}

Reload systemd daemon & start the unit.
{% highlight sh %}
systemctl daemon-reload
systemctl start mnt-backups.mount
{% endhighlight %}

And just like any other unit, you can view its status using `systemctl status mnt-backups.mount`:
{% highlight text %}
root@vast ~ # systemctl status mnt-backups.mount
● mnt-backups.mount - Mount System Backups Directory
   Loaded: loaded (/etc/systemd/system/mnt-backups.mount; enabled; vendor preset: disabled)
   Active: active (mounted) since Mon 2015-08-31 08:09:15 EAT; 2 days ago
    Where: /mnt/backups
     What: /dev/sdc
  Process: 744 ExecMount=/bin/mount /dev/disk/by-uuid/86fef3b2-bdc9-47fa-bbb1-4e528a89d222 /mnt/backups -n -t ext4 -o defaults (code=exited, status=0/SUCCESS)

Aug 31 08:09:15 vast systemd[1]: Mounting Mount System Backups Directory...
Aug 31 08:09:15 vast systemd[1]: Mounted Mount System Backups Directory.
{% endhighlight %}


### Gotchas!!

After a reboot, I noticed the unit wasn't started & as result the mount point dir. was empty. The unit file was missing an `[Install]` section which contains installation information such as unit dependencies(`WantedBy=, RequiredBy=`), aliases(`Alias=`), additional units(`Also=`), e.t.c for the specified unit. In this case, I set the unit to start in multi-user runlevel a.k.a `multi-user.target`. Oh, did you know you can change runlevel using `systemctl isolate $RUN_LEVEL.target`? [Read more](https://wiki.archlinux.org/index.php/Systemd#Targets_table) about systemd runlevels/targets.

Here's the complete `/etc/systemd/system/mnt-backups.mount` unit file with an `[Install]` section:
{% highlight ini %}
[Unit]
Description=Mount System Backups Directory

[Mount]
What=/dev/disk/by-uuid/86fef3b2-bdc9-47fa-bbb1-4e528a89d222
Where=/mnt/backups
Type=ext4
Options=defaults

[Install]
WantedBy=multi-user.target
{% endhighlight %}

As always, enable the unit to start automatically during boot.
{% highlight sh %}
systemctl enable mnt-backups.mount
{% endhighlight %}
