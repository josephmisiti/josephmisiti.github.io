---
layout: post
title:  "Installing Jammit on UBUNTU 14.04"
date:   2014-12-15
---

If you use Jammit for asset compression, it is sometimes (although not always) useful to deploy assets to the server and allow assets to be compressed there, before web server restart. Here is a fabric command that will do exactly that:

{% highlight python %}

def install_jammit():
	""" install jammit on ubuntu server """
	sudo("apt-get update")
	sudo("apt-get install -y default-jre")
	sudo("apt-get install -y ruby1.9.1")
	sudo("gem install jammit")

{% endhighlight %}

And to run it, you can execute something like:

```python 
fab install_jammit -R production 
```










