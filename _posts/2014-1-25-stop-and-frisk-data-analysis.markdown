---
layout: post
title:  "NYC Stop & Frisk - What does the data tell us ?"
date:   2014-1-25
categories: jekyll update
---

I spent a little time this weekend investigating the data around NYC's Stop-And-Frisk Policy. To start,
the dataset I downloaded can be found [here](http://www.nyclu.org/content/stop-and-frisk-data).

Disclaimer - I am against the stop and frisk policy.	

The question I am trying to answer is twofold

who are they targeting - and does that target make sense ?

First thing to notice, is there was a total of 532,912 stops in 2012:

```bash

josephmisiti$ cat data.csv | wc -l
  532912

```

That is a surprisng number of stops 1460/day (Police State Anyone?)