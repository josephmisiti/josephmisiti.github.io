---
layout: post
title:  'Automatic updates in ubuntu 1404 from the commandline'
date:   2016-07-18
categories: ubuntu,devops
---

Have you ever sshed into your server and seen the dreaded `XXX updates are security updates`?

{% highlight bash %}
Welcome to Ubuntu 14.04.4 LTS (GNU/Linux 3.13.0-73-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Mon Jul 18 21:28:50 UTC 2016

  System load:  0.0               Processes:           131
  Usage of /:   51.9% of 9.71GB   Users logged in:     1
  Memory usage: 45%               IP address for eth0: 10.0.1.41
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

89 packages can be updated.
62 updates are security updates.
{% endhighlight %}

There is an easy fix

{% highlight bash %}
sudo apt-get -y upgrade && sudo apt-get update && sudo reboot
{% endhighlight bash %}

And tell ubuntu you want automatic updates (select the Yes screen):

{% highlight bash %}
sudo dpkg-reconfigure --priority=low unattended-upgrades
{% endhighlight bash %}
