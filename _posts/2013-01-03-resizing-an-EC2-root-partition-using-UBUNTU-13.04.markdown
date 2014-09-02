---
layout: post
title:  "Resizing An EC2 Root Partition Running Ubuntu 13.04"
date:   2013-01-03
---

If you have a production computer on EC2 you should periodically check how much space the root partition has. By default, for Ubuntu AMIs Amazon provides 8GB of space, but as your data set, log files, etc continue to grow, you may experience a crash if your server runs out of space and your data base saves or log points start to fail. To see how much space you have, start by running the following command:

{% highlight bash %}
web1:~$ sudo df --si
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      8.5G  2.1G  6.0G  26% /
udev            3.9G  8.2k  3.9G   1% /dev
tmpfs           782M  189k  782M   1% /run
none            5.3M     0  5.3M   0% /run/lock
none            4.0G     0  4.0G   0% /run/shm
/dev/xvdb       444G  2.2G  420G   1% /mnt
{% endhighlight %}


As you can see, I am currently using 26% of the 8.5GB allocated by Amazon. The rest of the instructions will explain how to re-size this root partition and add extra space.

1. First things first, stop you instance from the EC2 managment console.

1. Go to volumes, track down the 8GB volume that is attached to the instance you just stopped, and detach it from the instance.

1. Once the volume has successfully detached, take a snapshot of it.

1. Once the snapshot has finished processing, create a new volume from the snapshot. You are obviously going to increase the size, but it is important to make sure the volume is in the same availablity zone as your stopped instance.

1. Once you have created the new volume, attach it to the stopped instance. It is important that you attached it to `/dev/sda1`, otherwise you will not be able to log back into your instance.

1. Once the volume is attached, re-start your instance and log back in. Do not forget that if you stopped your instance, the elastic IP has been un-associated from your instance, so you will need to re-associated it.

1. Once your back in, you can run df --si again, and you will see that the size hasnt actually changed - bummer. That is becuase you need to run the following command to finish off the process:

		{% highlight bash %}
		$ resize2fs /dev/xvda1
		{% endhighlight %}

( /dev/xvda1 is where you volume is mounted, and you can see that from looking at the output of df --si)

Finally, run df --si one last time and see the new, larger volume:

{% highlight bash %}
web1:~$ sudo df --si
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1      17.0G  2.1G  6.0G  13% /
udev            3.9G  8.2k  3.9G   1% /dev
tmpfs           782M  189k  782M   1% /run
none            5.3M     0  5.3M   0% /run/lock
none            4.0G     0  4.0G   0% /run/shm
/dev/xvdb       444G  2.2G  420G   1% /mnt
{% endhighlight %}
