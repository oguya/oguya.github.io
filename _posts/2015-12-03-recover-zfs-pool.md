---
layout: post
title:  "Recovering ZFS Pool"
date:   2015-12-04 08:10
excerpt: "Once in a while, more so after doing major system upgrades, you might end up losing your ZFS pool due to unkown circumstances. In this post I'll talk about how you can easily recover your pool.
Your / partition should NOT be on ZFS pool."
category: Linux
tags: zfs
---

So the other day I was upgrading my workstation from [Fedora 22 to Fedora 23](/linux/2015/12/02/upgrading-fedora/) and after the upgrade my ZFS pool disappeared! Oh BTW, I'm using [ZoL(ZFS on Linux)](http://zfsonlinux.org/).


## Troubleshooting

Normally the command `zpool status` would display detailed health status for the specified pool. But, if your pool is missing then you'll get something like:
{% highlight text %}
root@localhost ~ # zpool list
no pools available
root@localhost ~ # zpool status
no pools available
{% endhighlight %}

Scary, right??


## Quick Fix

Using `zpool import` we'll list all pools available & then import them.

> zpool import [-d dir | -c cachefile] [-D]
>
> Lists  pools available to import. If the -d option is not specified, this command searches for devices in "/dev".

- List all pools

{% highlight text %}
root@localhost ~ # zpool import
   pool: tank
     id: 11742341043733316809
  state: ONLINE
 action: The pool can be imported using its name or numeric identifier.
 config:

        tank                                                ONLINE
          raidz1-0                                          ONLINE
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part1  ONLINE
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part2  ONLINE
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part3  ONLINE
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part4  ONLINE
            11742341043733316809                            ONLINE

{% endhighlight %}

- Finally, import your pool, in this case, tank

{% highlight text %}
root@localhost ~ # zpool import tank
{% endhighlight %}

- All is well after all! No data lost :)
{% highlight text %}
root@localhost ~ # zpool status
  pool: tank
 state: ONLINE
  scan: scrub canceled on Tue Oct 27 08:15:07 2015
config:

        NAME                                                STATE     READ WRITE CKSUM
        tank                                                ONLINE       0     0     0
          raidz1-0                                          ONLINE       0     0     0
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part1  ONLINE       0     0     0
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part2  ONLINE       0     0     0
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part3  ONLINE       0     0     0
            ata-WDC_WD30EFRX-68EUZN0_WD-WMC4N0824448-part4  ONLINE       0     0     0
            11742341043733316809                            ONLINE       0     0     0

errors: No known data errors

root@localhost ~ # zpool list
NAME   SIZE  ALLOC   FREE  EXPANDSZ   FRAG    CAP  DEDUP  HEALTH  ALTROOT
tank  2.44T   143G  2.30T         -     5%     5%  1.07x  ONLINE  -

root@localhost ~ # zfs list
NAME             USED  AVAIL  REFER  MOUNTPOINT
tank             123G  1.77T   166K  /mnt/zpool/tank
tank/backups     117G   383G   117G  /mnt/zpool/tank/backups
tank/deadhorse  4.34G   496G  4.34G  /mnt/zpool/tank/deadhorse
{% endhighlight %}
