---
layout: post
title:  "Introduction To Pandas Indexes and Operations"
date:   2014-12-14
---

In the previous [blog post](http://josephmisiti.github.io/introduction-to-pandas-data-structures/), we went over the two main panda's data structures: Series and Dataframe. In this blog post, we are going to take things a step further and introduce you to Panda's indexes and the operations you can perform on them.

First, let's initial a series with a character-based index (which we used in the previous blog post)

{% highlight python %}

In [6]: s3 = Series([1,2,3,4],index=['a','b','c','d'])

{% endhighlight %}

One very useful feature is that indexes can be used the same way list index/numpy array indexes, except with non-integer values:

{% highlight python %}

In [7]: s3
Out[7]:
a    1
b    2
c    3
d    4
dtype: int64

In [8]: s3[['a']]
Out[8]:
a    1
dtype: int64

In [9]: s3[['a','b']]
Out[9]:
a    1
b    2
dtype: int64

{% endhighlight %}

They also support list/array like slicing, but again, on non-integer values:

{% highlight python %}

In [11]: s3['a':'b']
Out[11]:
a    1
b    2
dtype: int64

{% endhighlight %}











