---
layout: post
title:  "Formatting An EBS Drive"
date:   2014-10-06
categories: amazon,dev-ops
---

I ran into a small issue trying to mount an EBS drive today, I figured it was worth documenting.
After attaching an EBS drive I created to my instance, I tried to mount the drive:

```
db1:~$ sudo mount /ebs/
```

And got the following error (which I have never seen before):

{% highlight python%}
db1:~$ sudo mount /ebs/
mount: wrong fs type, bad option, bad superblock on /dev/xvdb,
       missing codepage or helper program, or other error
       In some cases useful info is found in syslog - try
       dmesg | tail  or so
{% endhighlight %}


I looked around multiple sites and ended up with the following solution:


{% highlight python%}
db1:~$ sudo mkfs.ext3 /dev/xvdb
mke2fs 1.42.9 (4-Feb-2014)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
Stride=0 blocks, Stripe width=0 blocks
6553600 inodes, 26214400 blocks
1310720 blocks (5.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=4294967296
800 block groups
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
	4096000, 7962624, 11239424, 20480000, 23887872
 
Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done
 
db1:~$ sudo mount /ebs/
{% endhighlight %}

After running mkfs (make filesystem), I could successfully mount my volume.
