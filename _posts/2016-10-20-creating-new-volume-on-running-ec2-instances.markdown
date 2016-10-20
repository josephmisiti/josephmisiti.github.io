---
layout: post
title:  'Creating New Volumes on Running EC2 Instances'
date:   2016-10-20
categories: jekyll update
---

Did your web service go down because the 10GB amazon gives you on a new instance has been depleted ?
Here is a quick fix to solving that problem.

First, you can use the `df` command to take inspect the current volumes and space allocations:

````
ubuntu@vr-prod-task1:/srv/www/ventus$ sudo df --si
Filesystem      Size  Used Avail Use% Mounted on
udev            2.1G   13k  2.1G   1% /dev
tmpfs           415M  390k  415M   1% /run
/dev/xvda1       11G  5.4G  4.6G  54% /
none            4.1k     0  4.1k   0% /sys/fs/cgroup
none            5.3M     0  5.3M   0% /run/lock
none            2.1G  4.1k  2.1G   1% /run/shm
none            105M     0  105M   0% /run/user
``` 

Next, use `fdisk` to find which volumes are currently attached where they are attached:

```
ubuntu@vr-prod-task1:/srv/www/ventus$ sudo fdisk -l

Disk /dev/xvda: 10.7 GB, 10737418240 bytes
255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *       16065    20964824    10474380   83  Linux
```

To create a new volume, log in to ec2 and look at which AZ your instance is in. Next go to the volume section and create a new volume, picking the parameters of your choosing, and once EC2 has created that volumne, attach it do your instance.

Now run `fdisk` again and you should see your new volume:

```
ubuntu@vr-prod-task1:/srv/www/ventus$ sudo fdisk -l

Disk /dev/xvda: 10.7 GB, 10737418240 bytes
255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

    Device Boot      Start         End      Blocks   Id  System
/dev/xvda1   *       16065    20964824    10474380   83  Linux

Disk /dev/xvdf: 107.4 GB, 107374182400 bytes
255 heads, 63 sectors/track, 13054 cylinders, total 209715200 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

Disk /dev/xvdf doesn't contain a valid partition table
```

In this example, I attached `/dev/xvdf` to the instance, it was 100GB.

Next, reformat the volume:

```
sudo mkfs -t ext4 /dev/xvdf
```

Decide where you want it to live on disk and create that directory (I choose `/vol1` in this example):

```
mkdir /vol1
```

Mount it

```
sudo mount /dev/xvdf /vol1
```

Finally, update (`sudo vim  /etc/fstab`) the `fstab` file with the following line:

```
dev/xvdf       /vol1   auto    defaults,nobootwait     0       0
```

Reboot and your done!

```
ubuntu@vr-prod-task1:/srv/www/ventus$ df --si
Filesystem      Size  Used Avail Use% Mounted on
udev            2.1G   13k  2.1G   1% /dev
tmpfs           415M  390k  415M   1% /run
/dev/xvda1       11G  5.4G  4.6G  54% /
none            4.1k     0  4.1k   0% /sys/fs/cgroup
none            5.3M     0  5.3M   0% /run/lock
none            2.1G  4.1k  2.1G   1% /run/shm
none            105M     0  105M   0% /run/user
/dev/xvdf       106G  4.8G   96G   5% /vol1
```