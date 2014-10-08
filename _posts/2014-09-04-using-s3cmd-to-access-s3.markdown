---
layout: post
title:  "Using S3cmd To Access Files on Amazon S3"
date:   2014-09-04
categories: amazon,s3,web,cli
---

I know I'm late to the game on this one, but I recently found [s3cmd](http://s3tools.org/s3cmd), a command line interface on to Amazon S3, and life got a little bit easier.

To download `s3cmd` using brew, execute the following command:

{% highlight python %}
brew install s3cmd
{% endhighlight %}


Once `s3cmd` is installed, you need to configure it with your EC2 access keys:

{% highlight python %}
s3cmd --configure
{% endhighlight %}


The access keys can be found in the securities section of your EC2 management console.

Once you have the command configured, lets run a UNIX-style `ls` of your S3 buckets:

{% highlight python %}
JOSEPH-MISITI:~ josephmisiti$ s3cmd ls
2013-11-10 02:34  s3://buttonbox-developement
2014-01-06 18:47  s3://dukemail-developement
2013-11-08 02:36  s3://elementcycle
2014-01-22 04:22  s3://dev-getfetcher
...
{% endhighlight %}


If you want to create a new bucket, you can execute the create bucket command:

{% highlight python %}
JOSEPH-MISITI:~ josephmisiti$ s3cmd mb s3://mathandpencil-example
Bucket 's3://mathandpencil-example/' created
{% endhighlight %}

Now lets copy a zip file to the new bucket:

{% highlight python %}
JOSEPH-MISITI:~ josephmisiti$ s3cmd put ~/Downloads/tdd-backbone-mocha.zip s3://mathandpencil-example
/Users/josephmisiti/Downloads/tdd-backbone-mocha.zip -> s3://mathandpencil-example/tdd-backbone-mocha.zip  [1 of 1]
 144871 of 144871   100% in    1s   122.43 kB/s  done
JOSEPH-MISITI:~ josephmisiti$ s3cmd ls s3://mathandpencil-example
2014-09-05 03:41    144871   s3://mathandpencil-example/tdd-backbone-mocha.zip
{% endhighlight %}

And lets make that zip file publicly accessible:

{% highlight python %}
JOSEPH-MISITI:~ josephmisiti$ s3cmd setacl --acl-public --recursive s3://mathandpencil-example
s3://mathandpencil-example/tdd-backbone-mocha.zip: ACL set to Public  [1 of 1]
{% endhighlight %}

Finally, lets delete the zip file and then the bucket itself:

{% highlight python %}
JOSEPH-MISITI:~ josephmisiti$ s3cmd del s3://mathandpencil-example/tdd-backbone-mocha.zip
File s3://mathandpencil-example/tdd-backbone-mocha.zip deleted
JOSEPH-MISITI:~ josephmisiti$ s3cmd rb s3://mathandpencil-example/
Bucket 's3://mathandpencil-example/' removed
{% endhighlight %}
That's it! It is an amazing tool that makes script and accesing S3 much easier and enjoyable.
