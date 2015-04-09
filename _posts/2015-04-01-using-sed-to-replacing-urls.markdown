---
layout: post
title:  "Using SED to replace urls in a list of files"
date:   2015-04-01
---

Say you have a list of files on your harddrive and you want to replace

```
/api/hello.json
```

with 

```
/api/v2/hello.json
```

This can be done very easily with `sed`:

{% highlight bash %}
for f in ./swagger/v2/api/v2/*.json; do
	echo $f
	sed -i -e s/api\\//api\\/v2\\// $f
done
{% endhighlight %}

This command will overwrite your files and replace that URL.