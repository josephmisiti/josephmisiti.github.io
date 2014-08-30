---
layout: post
title:  "Using Latent Dirichlet Allocation to Categorize My Twitter Feed"
date:   2014-06-29
---

Over the past 3 years, I have tweeted about  4100 times, mostly URLS, and mostly about machine learning, statistics, big data, etc. I spent some time this past weekend seeing if I could categorize the tweets using Latent Dirichlet Allocation. For a great introduction to Latent Dirichlet Allocation (LDA), you can read the following link  [here](http://blog.echen.me/2011/08/22/introduction-to-latent-dirichlet-allocation/). For the more mathematically inclined, you can read through this [excellent paper](http://www.semanticsearchart.com/downloads/UWEETR-2010-0006.pdf) which explains LDA in a lot more detail.

The first step to categorizing my tweets was pulling the data. I initially downloaded and installed Twython and tried to pull all of my tweets using the Twitter API, but that quickly realized there was an archive button under settings. So I stopped writing code and just double clicked the archive button. Apparently 4100 tweets is fairly easy to archive, because I received an email from Twitter within 15 seconds with a download link.

After downloading my Twitter archive, I opened up tweets.csv (provided by twitter) and extracted the single column containing the tweets to a new file for additional processing. I did this using unix awk:

```
cat tweets.csv | awk -F"," '{print $6}' > tweets_content.dat
```

Next, I needed to extract the URLs out of the tweets (I do not care about the content for this type of analysis). To extract the URLs, I wrote a simple Python script:

{% highlight python %}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import re
import os
import sys

for line in open(sys.argv[1]).readlines():
	results = re.search("(?P<url>https?://[^\s]+)", line.strip())
	if results:
		print results.group("url")
{% endhighlight %}


After running this script across tweets_content.dat, I am left with a file what contains only the URLS I tweeted out. Here is a [gist](https://gist.github.com/josephmisiti/782f8433b393069dfe4c) of all of the URLS I found. 

Once I had the URLS, I needed to be able to extract the information in the URLS to build a corpus for LDA analysis. Unfortunately, this was not as easy as I thought it was going to be. I initially started using Python's excellent NLTK library to extract the HTML using the following command:

{% highlight python %}
nltk.clean_html(urlopen(url).read())
{% endhighlight %}

After spot checking the results, I realized that this was not successfully retrieving HTML for about 1/3 of my URLS.  I did some more research and found [DiffBot](https://www.diffbot.com/), which uses machine learning to figure out what content is valid and removes ads, etc. They will give you 10K calls/day for free, so I used their [bulk API](http://diffbot.com/dev/docs/bulk/) to retrieve the content of my URLS (parsed HTML). The bulk API call looks something like this.

{% highlight python %}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import os
import sys
import requests
import json

DIFF_BOT_TOKEN = 'xxxxxxxx'

def create_bulk_job(urls, name):
	apiUrl = 'http://api.diffbot.com/v3/article'
	params = dict(token=DIFF_BOT_TOKEN, name=name, urls=urls, apiUrl=apiUrl)
	response = requests.post('http://api.diffbot.com/v3/bulk',data=params)
	return json.loads(response.content)
	
if __name__ == '__main__':
	urls = " ".join([u.strip().replace('"','') for u in open(sys.argv[1]).readlines()])
	name = 'MISITI'
	create_bulk_job(urls=urls,name=name)
{% endhighlight %}

After about 10 minutes, DiffBot was able to crawl all of the URLs I sent it, and I downloaded the results into JSON format. Now that I had the html from each of the URLs, I used the diffbot html to make my corpus. I didn't use any sophisticated techniques to create the corpus (although I think you could add some here to see better performance) - I wrote a quick script that extracted only words, made them lower case, and then remove all English stop words using NLTK.

{% highlight python %}
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import re
import os
import sys
import json
import nltk

STOP_WORDS = nltk.corpus.stopwords.words('english')
CORPUS_FILE = open("corpus.txt", "w")

tweets = json.loads(open("results.json").read())
for tweet in tweets:
	html = tweet.get("text","")
	words = html.split()
	words = [x.lower() for x in words if re.match('^[\w-]+$', x) is not None] # take only alphanumerics
	words = [word.lower() for word in words]
	words = [word for word in words if word not in STOP_WORDS]
	
	CORPUS_FILE.write(" ".join(words) + "\n")
	
CORPUS_FILE.close()

{% endhighlight%}

After the corpus was created, I was able to run LDA on the data set. The inputs to the LDA process are the number of topics K, the corpus file (corpus.txt in this case), and priors for the alpha/beta distributions. I used default priors and ran LDA for 50 iterations. The whole process took about 10 minutes and my results can be seen in 10 gists below. I searched K from 5 - 20 and 10 topics yielded the best results. I did not take into account perplexity or any other metrics for making my decision. A spot check of the top words in each topic yielded what appeared to be the following categories.

[Topic 1](https://gist.github.com/josephmisiti/3ed68c02d3d6877a3097) - social media / tech companies

[Topic 2](https://gist.github.com/josephmisiti/2a7a6f58f0b4e1dbdb43) - generic

[Topic 3](https://gist.github.com/josephmisiti/daebfd66d913dc426ec8) - software / cloud computing / search

[Topic 4](https://gist.github.com/josephmisiti/45b31c52167af8c37735) - cities

[Topic 5](https://gist.github.com/josephmisiti/061042ecd684b1831e32) - government / nsa  / intelligence

[Topic 6](https://gist.github.com/josephmisiti/b4a8e97dd2cc09d0d367) - banking / finance / economics

[Topic 7](https://gist.github.com/josephmisiti/037141fdf95c887cf6a0) - cloud computing / ec2 / the big lebowski

[Topic 8](https://gist.github.com/josephmisiti/dc94723022d4e5630f7c) - world events

[Topic 9](https://gist.github.com/josephmisiti/8be39c0b6fd4a353a80e) - machine learning / big data / statistics

[Topic 10](https://gist.github.com/josephmisiti/50915dda4f745c572905) - religion / sexuality

The final step to the process was calculating the topic distriubtion of each url/document. This can be done by simply summing the topic distribution vectors that correspond to each word in the document. These vectors are one of the outputs of the LDA process. After the vector sums are calculated, you normalize each vector (because they are, after all, a probability mass function). I used the following code to calculate the distributions of each url.

{% highlight python %}
import numpy as np
import cPickle as pickle
import json

(ldak, phi, voca) = pickle.load(open('ldaphi_K10.p', "rb"))

words_to_column = {}
for i,w in enumerate(voca):
	words_to_column[w] = i
		
tweets = json.loads(open("results.json").read())
for tweet in tweets:
	html = tweet.get('text')
	title = tweet.get('title','').encode("ascii","ignore")
	url = tweet.get('resolvedPageUrl',"").encode("ascii","ignore")
	
	sum_vector = np.zeros(ldak)
	for word in html.split():
		word = word.lower()
		if word in words_to_column:
			sum_vector += phi[:, words_to_column[word]]
	if sum_vector.sum() == 0: 
		sum_vector = np.ones(ldak)/ldak;
	else: 
		sum_vector = sum_vector / sum_vector.sum()
		
	print "%s,%s,%s" %(",".join(["%2.3f" % s for s in sum_vector]),title,url)	
	
{% endhighlight %}

I uploaded the results in CSV format to Google Drive and anyone can download the data [here](https://docs.google.com/spreadsheets/d/1iwvCYeEEOF-4BtLf7gKUAA638fyftdlcgETpZeRYcP8/edit?usp=sharing). You can sort A-Z on any of the 10 topic columns and theoretically the appropriate URLS should float to the top. This process is not perfect, but I was pretty impressed with the results. I think if I tweeted a lot more, the number of unique words in my corpus (the vocabulary) would be much larger and would probably lead to better segmentation. In a future blog post, I plan on taking perplexity and the priors into account also.

If you are interested in machine learning, math, and statistics you can following me on twitter: [@josephmisiti](https://www.twitter.com/josephmisiti) - I spend all day tweeting about it.

If you need help with your data set, contact my firm at info@mathandpencil.com

You can clone this [repo](https://github.com/mathandpencil/twitter-lda), but just a warning, I did not take anytime to organize the code or the results. 