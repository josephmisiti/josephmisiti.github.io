---
layout: post
title:  "Statistics in MongoDB"
date:   2013-12-26 18:19:13
categories: jekyll update
---

I am creating this blog post as a quick reminder of how to check the size of my database:

{% highlight ruby %}
> db.stats(1024)
 
{
	"db" : "sqdev",
	"collections" : 25,
	"objects" : 31509618,
	"avgObjSize" : 704.2919142974059,
	"dataSize" : 21671844,
	"storageSize" : 24783691,
	"numExtents" : 163,
	"indexes" : 44,
	"indexSize" : 25900801,
	"fileSize" : 64915456,
	"nsSizeMB" : 16,
	"ok" : 1
}
{% endhighlight %}

Per the documentation @ 10Gen, the results of this command return kilobytes[1]. So just divide

everything by (1024^2) to get gigobytes.

31509618/(1024^2) = ~31Gs of data

25900801/(1024^2) = ~25G of indexes (WTF??)

[1] http://docs.mongodb.org/manual/reference/database-statistics/