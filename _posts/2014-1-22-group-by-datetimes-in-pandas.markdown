---
layout: post
title:  "Grouping By Day, Week and Month with Pandas DataFrames"
date:   2014-1-22
categories: jekyll update
---

This maybe useful to someone besides me. I had a dataframe in the following format:

```
0,2013-07-15 17:15:19,1,8872,291840,92
1,2011-07-19 23:26:24,2,18890,760336,0
2,2011-07-26 22:58:35,2,2902,76746,0
3,2011-07-28 22:27:12,2,103222,954442,0
4,2011-07-29 21:26:27,2,107134,885380,0
5,2011-07-31 04:11:38,2,44228,813568,0
6,2011-08-02 23:17:39,2,109242,1157330,0
7,2011-08-03 22:14:08,2,51736,870914,0
8,2011-08-04 21:41:33,2,18390,652704,0

```

And I wanted to sum the third column by day, wee and month. There are multiple reasons why you can just read in
this code with a simple

```
df = pd.read_csv(file)
```

And go to town. First we need to change the second column (_id) from a string to a python datetime object to run the analysis:

```
import pandas as pd
import numpy as np

df = pd.read_csv(file,sep=',')
df["_id"] = pd.to_datetime(df["_id"])

```

OK, now the _id column is a datetime column, but how to we sum the count column by day,week, and/or month? First, we need to change the pandas default index on the dataframe (int64). You can find out what type of index your dataframe is using by using the following command

```
print(df.index)
```

To perform this type of operation, we need a pandas.DateTimeIndex and then we can use pandas.resample, but first lets strip modify the _id column because I do not care about the time, just the dates.

```
df['date_minus_time'] = df["_id"].apply( lambda df : 
datetime.datetime(year=df.year, month=df.month, day=df.day))	
df.set_index(df["date_minus_time"],inplace=True)
```

Finally, if you want to group by day, week, month respectively:

```
df['count'].resample('D', how='sum')
df['count'].resample('W', how='sum')
df['count'].resample('M', how='sum')

```


