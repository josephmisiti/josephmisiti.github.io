---
layout: post
title:  "Running Jammit on Ubuntu Natty 11.04"
date:   2013-01-20
---

I like to use DocumentCloud's [Jammit](http://documentcloud.github.com/jammit/) for CSS/JS compression. I have been doing the compression locally and then unzipping the files on the server, but I recently wanted to change this and do the compression on the server -- that way CSS/JS and Python could NEVER get out of sync (say if I was on branch X but deployed branch Y locally, the server would have a branch Y django backend but a branch X front-end). When I moved the compression to the server, I saw the following message (deploying using [Fabric](http://docs.fabfile.org/en/1.4.3/index.html)):

    [xxx.xxx.xxx.xxx] out: Jammit Warning: Asset compression disabled -- Java unavailable.

Sure enough, the JS/CSS was not being compressed (not cool).

Here is the fix incase anyone is interested:

{% highlight python %}
sudo add-apt-repository ppa:ferramroberto/java
sudo apt-get update
sudo apt-get -f install sun-java6-jdk
{% endhighlight %}

which tells me to run this:
 
{% highlight python %}
sudo apt-get -f install
sudo apt-get install sun-java6-jdk
{% endhighlight %}

Now Java is installed on your instance and Jammit will compress your assets correctly!

