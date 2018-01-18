---
layout: post
title:  "Removing Shitty Line Endings With VIM"
date:   2018-01-18
---

Recently I had an issue where someone had checked in `^M` line ends to a few files into github. When
I open the file in atom, I see something like the following:

{% highlight python %}
import loggingfrom django.db import modelslogger = logging.getLogger(__name__)all_filters =
{% endhighlight %}

To fix this, simply open `vim` and execute the following command: `%s/\r/\r/g`
