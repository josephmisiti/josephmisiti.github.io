---
layout: post
title:  "Compiling LIBFFM On OSX 10.9"
date:   2015-03-02
categories: c++
---

I recently tried to compile [LIBFFM](http://www.csie.ntu.edu.tw/~r01922136/libffm/) (Field-aware Factorization Machines) on my local machine running 10.9 and things did not work out as expected:

{% highlight bash %}
fmm.cpp:11:10: fatal error: 'omp.h' file not found
#include <omp.h>
         ^
1 error generated.
{% endhighlight %}

So it turns out the version of GCC that ships with OSX does not support this [header](https://discussions.apple.com/message/25735227)

I ended up getting it compiled, and here is how I did it:

First, download a new version of GCC:

{% highlight bash %}
wget http://prdownloads.sourceforge.net/hpc/gcc-4.9-bin.tar.gz
{% endhighlight %}

Next, upzip and untar it, and put it in /usr/local/bin

{% highlight bash %}
gunzip gcc-4.9-bin.tar.gz
sudo tar -xvf gcc-4.9-bin.tar -C /
{% endhighlight %}

Now, you need to update the `Makefile` that comes with LIBFFM to look like this

{% highlight bash %}
CXX = g++
CXXFLAGS = -Wall -O3 -std=c++0x

# uncomment the following flags if you do not want to use OpenMP
DFLAG += -DUSEOMP
CXXFLAGS += -fopenmp

all: ffm-train ffm-predict

ffm-train: ffm-train.cpp ffm.o
	$(CXX) $(CXXFLAGS) -o $@ $^

ffm-predict: ffm-predict.cpp ffm.o
	$(CXX) $(CXXFLAGS) -o $@ $^

ffm.o: ffm.cpp ffm.h
	$(CXX) $(CXXFLAGS) $(DFLAG) -c -o $@ $<

clean:
	rm -f ffm-train ffm-predict ffm.o timer.o

{% endhighlight %}

Finally, run `make` and you are good to go

{% highlight bash %}

(vor)JOSEPH-MISITI:libffm-1.0 josephmisiti$ make
g++ -Wall -O3 -std=c++0x -fopenmp -DUSEOMP -c -o ffm.o ffm.cpp
g++ -Wall -O3 -std=c++0x -fopenmp -o ffm-train ffm-train.cpp ffm.o
g++ -Wall -O3 -std=c++0x -fopenmp -o ffm-predict ffm-predict.cpp ffm.o
{% endhighlight %}
