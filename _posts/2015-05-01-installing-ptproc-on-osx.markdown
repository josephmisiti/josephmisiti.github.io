---
layout: post
title:  "Installing ptproc on OSX"
date:   2015-05-01
---

Do you want to model some event data using [Point Processes?](http://en.wikipedia.org/wiki/Point_process). I did too, and unfortunately tried to use R to do this.

```
install.packages("ptproc")
```

```
package ‘ptproc’ is not available (as a binary package for R version 3.1.3) 
```

Great!. So I go [here](http://www.biostat.jhsph.edu/~rpeng/software/) and download the `tar.gz` file and try to install from source:

{% highlight R %}

> install.packages("ptproc",  repos="http://www.biostat.jhsph.edu/~rpeng/software", 
type="source")
trying URL 'http://www.biostat.jhsph.edu/~rpeng/software/src/contrib/ptproc_1.5-1.tar.gz'
Content type 'application/x-gzip' length 282002 bytes (275 KB)
opened URL
==================================================
downloaded 275 KB

* installing *source* package ‘ptproc’ ...
ERROR: a 'NAMESPACE' file is required
* removing ‘/Library/Frameworks/R.framework/Versions/3.1/Resources/library/ptproc’

The downloaded source packages are in
	‘/private/var/folders/0b/qdw3f3zn0gq5yy2cjjpm8cgw0000gn/T/RtmpuW1EPA/downloaded_packages’
Warning message:
In install.packages("ptproc", repos = "http://www.biostat.jhsph.edu/~rpeng/software",  :
  installation of package ‘ptproc’ had non-zero exit status

{% endhighlight%}

The solution that ended up working was download it manually, add the NAMESPACE file, re-zip,  and install:

{% highlight bash %}
1  cd ptproc
2  echo 'exportPattern( "." )' > NAMESPACE
3  cd ../
4  rm ptproc_1.5-1.tar.gz
5  tar cvzf ptproc/ ptproc_1.5-1.tar.gz
{% endhighlight%}

I ended up installing the package via RStudio (TOOLS->Install Packages), but it runs from the regular R REPL now also.	
