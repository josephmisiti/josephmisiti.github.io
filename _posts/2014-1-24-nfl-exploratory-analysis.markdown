---
layout: post
title:  "NFL Exploatory Analysis"
date:   2014-1-24 18:19:13
categories: jekyll update
---

I recently saw [this](https://github.com/devstopfix/nfl_results) github repo get starred, so I figured I'd check out what is in here. 

First off, lets concatenate the whole file with a bash script (all my CSVs are in a data directory):

```bash

for f in ./data/*.csv; do 
	echo "processing $f"
	cat "$f" | grep -v '^s' >> ./data/all.csv
done

````

OK .. cool, now lets bust out pandas and see what is here

![My helpful screenshot](/assets/image1.png)

The Denver Brancos score an average of 25.1 points at home from 1964-2013

Now lets look at the visiting team

![My helpful screenshot](/assets/image2.png)