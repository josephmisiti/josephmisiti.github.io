---
layout: post
title:  "Adding Swap Space on Ubuntu 14.04"
date:   2015-08-25
categories: ios
---

Recently, I ran into the following error while installing some software on Ubuntu 14.04:

```
virtual memory exhausted: Cannot allocate memory
```

Not good - but the fix is easy -- add some swap space to your instance. The following
is a list of commands you can use to successfully resolve this problem:


{% highlight bash %}

85  cd /
87  sudo dd if=/dev/zero of=swapfile bs=1M count=3000
88  sudo mkswap swapfile
89  sudo vi /etc/fstab

{% endhighlight %}

Now add the following like to `/etc/fstab`:

```
/swapfile none swap sw 0 0
```

And do not forgot to reboot .....

{% highlight bash %}
   95  sudo reboot
{% endhighlight %}

How lets make sure the swap space exists:

{% highlight bash %}
ubuntu@ip-172-30-0-233:~$ cat /proc/meminfo | grep '[Ss]wap'
SwapCached:         3720 kB
SwapTotal:       3071996 kB
SwapFree:        3059708 kB
{% endhighlight %}

As you can see, you now have 3GB of swap space on your device !!! Not - EC2 comes with ZERO swap 
space by default - so it might be no a surprise if you are seeing this issue - especially on a `t2.micro`
instance.