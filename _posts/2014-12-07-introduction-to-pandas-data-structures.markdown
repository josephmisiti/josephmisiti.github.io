---
layout: post
title:  "Introduction To Pandas Data Structures"
date:   2014-12-07
---

Here are some quick notes on two of Pandas data structures: Series and Data Frame.

#### Series

A series is a one-dimensional container containing an array of data and its corresponding labels (its index).

There are multiple different ways to create or initialize a Pandas series:

From an existing python list or numpy array

```python
s = Series([1,2,3,4,5,6,7])
```

or


```python
s1 = Series(np.array([1,2,3,4,5,6,7]))
```

If you do not provide an index, the Series will default index is provide consisting of integers 0 to N-1 where N is the length of your data

```
In [8]: s.index
Out[8]: Int64Index([0, 1, 2, 3, 4, 5, 6], dtype='int64')
```

You can also initialize a Series with a dict (you could even think of a Series as an ordered-dict):

{% highlight python %}

In [9]: data = { 'a' : 1, 'b' : 2, 'c' : 3, 'd': 4 }

In [10]: s2 = Series(data)

In [11]: s2.index
Out[11]: Index([u'a', u'b', u'c', u'd'], dtype='object')

{% endhighlight %}

Finally, you can initialize a series by explicitly defining the data and the index:

{% highlight objc %}
In [12]: s3 = Series([1,2,3,4],index=['a','b','c','d'])

In [13]: s3
Out[13]:
a    1
b    2
c    3
d    4
dtype: int64

{% endhighlight %}

A series index can be altered in place also:

{% highlight python %}

In [14]: s2
Out[14]:
a    1
b    2
c    3
d    4
dtype: int64

In [15]: s2.index = ['d','c','b','a']

In [16]: s2
Out[16]:
d    1
c    2
b    3
a    4
dtype: int64
{% endhighlight %}

#### Data Frames
	
Pandas Data Frames are like matrices where each column can be of a different type. As it can be thought of as an dictionary of Series, you can initailize a Data Frame using a dictionary of python lists:

{% highlight python %}

In [22]: data = {'a' : [1,2,3], 'b' : [4,5,6], 'c' : [7,8,9]}

In [23]: df = DataFrame(data)

In [24]: df
Out[24]:
   a  b  c
0  1  4  7
1  2  5  8
2  3  6  9

In [25]: df.index
Out[25]: Int64Index([0, 1, 2], dtype='int64')

{% endhighlight %}

Again, you will see that if you do not provide an index, it defaults to the same standard integer index as the Series data structure does.
You can specify the index in the Data Frame constructor.

{% highlight python %}

In [26]: df = DataFrame(data,index=['aa','bb','cc'])

In [27]: df
Out[27]:
    a  b  c
aa  1  4  7
bb  2  5  8
cc  3  6  9


{% endhighlight %}

You can access a single column or multiple columns using the following format


{% highlight python %}

n [28]: df['a']
Out[28]:
aa    1
bb    2
cc    3
Name: a, dtype: int64

In [29]: df[['a','c']]
Out[29]:
    a  c
aa  1  7
bb  2  8
cc  3  9


{% endhighlight %}

Column names can be changed in place, or specified using the `column` attribute in the Data Frame constructor:


{% highlight python %}

In [31]: df.columns = ['col1','col2','col3']

In [32]: df
Out[32]:
    col1  col2  col3
aa     1     4     7
bb     2     5     8
cc     3     6     9


{% endhighlight %}

Rows in a Data Frame can be accessed a number of different ways:

Using the `ix` attribute:

{% highlight python %}

In [32]: df
Out[32]:
    col1  col2  col3
aa     1     4     7
bb     2     5     8
cc     3     6     9

In [33]:

In [33]: df.ix['aa']
Out[33]:
col1    1
col2    4
col3    7
Name: aa, dtype: int64

{% endhighlight %}

Adding a new column is pretty easy also. For example, if we want to create a new column of a single value `3.14`, you can do that like this

{% highlight python %}

In [34]: df['pi'] = 3.14

In [35]: df
Out[35]:
    col1  col2  col3    pi
aa     1     4     7  3.14
bb     2     5     8  3.14
cc     3     6     9  3.14

{% endhighlight %}

Or, you can create a new column using a list/np.array as long as you keep the dimensionality correct:

{% highlight python %}

In [37]: df['pi'] = range(3)

In [38]: df
Out[38]:
    col1  col2  col3  pi
aa     1     4     7   0
bb     2     5     8   1
cc     3     6     9   2

{% endhighlight  %}

And check this out, can you transpose a Data Frame by using the `T` property


{% highlight python %}

In [39]: df.T
Out[39]:
      aa  bb  cc
col1   1   2   3
col2   4   5   6
col3   7   8   9
pi     0   1   2

{% endhighlight %}

You can also easily export the Data Frame to json:

{% highlight python %}

In [41]: df.to_json()
Out[41]: '{"col1":{"aa":1,"bb":2,"cc":3},
"col2":{"aa":4,"bb":5,"cc":6},
"col3":{"aa":7,"bb":8,"cc":9},"pi":{"aa":0,"bb":1,"cc":2}}'

{% endhighlight %}

#### Re-indexing 

Re-indexing is a pretty useful feature than can be applied to both Series and Data Frames. Reindexing creates a new object
which conforms to a new index:

{% highlight python %}


In [46]: s2
Out[46]:
d    1
c    2
b    3
a    4
dtype: int64

In [47]: s3 = s2.reindex(['a','b','c','d','e','f','g'])

In [48]: s3
Out[48]:
a     4
b     3
c     2
d     1
e   NaN
f   NaN
g   NaN
dtype: float64


{% endhighlight %}






