---
layout: post
title:  "Adding an EBS Volume to a running instance on AWS"
date:   2016-03-10
---

If you need to add some more space to an instance running on EC2, and that instance is running the latest 
LTS version of UBUNTU. It is fairly easy to do.

First, create an instance in the management console of AWS and attach it to your instance. Note - you need
to create the EBS in the same AZ that your instance is running. After you EBS has been attached, you 
need to see where AWS mounted your image on your instance. Login to your instance and check it out

{% highlight bash %}
ubuntu@ip-1-1-1-93:/vol$ sudo fdisk -l

Disk /dev/xvda: 8589 MB, 8589934592 bytes
255 heads, 63 sectors/track, 1044 cylinders, total 16777216 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *       16065    16771859     8377897+  83  Linux

Disk /dev/xvdf: 268.4 GB, 268435456000 bytes
255 heads, 63 sectors/track, 32635 cylinders, total 524288000 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/xvdf doesn't contain a valid partition table
{% endhighlight %}

As you can see from the above output, I created a new 250 GB EBS and AWS has attached it at `/dev/xvdf`. Now that
it is attached, you need to format it. I recommend using [ext4](https://en.wikipedia.org/wiki/Ext4) file extension,
but what you use may depend on performance requirements.

Assuming you are going with `ext4`, you need to report the disk using the following command:

{% highlight bash %}
sudo mkfs.ext4 /dev/xvdf
{% endhighlight %}

Reformatting should only take a few minutes, depending on the size of your EBS. Once it is reformatted, you need to update
the `/etc/fstab` file to tell UBUNTU about your new disk and where it is mounted.

{% highlight bash %}
sudo mkdir -m 000 /vol
echo "/dev/xvdf /vol auto noatime 0 0" | sudo tee -a /etc/fstab
{% endhighlight %}

Save `fstab` and now you should be able to mount your disk successfully

{% highlight bash %}
sudo mount /vol
{% endhighlight %}

Finally, confirm your new EBS is working properly.

{% highlight bash %}
ubuntu@ip-1-1-1-93:/vol$ df --si
Filesystem      Size  Used Avail Use% Mounted on
udev            4.2G   13k  4.2G   1% /dev
tmpfs           838M  357k  837M   1% /run
/dev/xvda1      8.4G  1.2G  6.8G  15% /
none            4.1k     0  4.1k   0% /sys/fs/cgroup
none            5.3M     0  5.3M   0% /run/lock
none            4.2G     0  4.2G   0% /run/shm
none            105M     0  105M   0% /run/user
/dev/xvdf       265G  4.0G  247G   2% /vol

{% endhighlight %}

Done