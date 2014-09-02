---
layout: post
title:  "Installing OpenCv on OSX"
date:   2013-02-14
---

#### Installation From Source

All of the install guides I read were telling me to use macports or brew to install OpenCV. Neither method worked, so I had to resort to building from source, which turned out to be the easiest of the three methods:

{% highlight bash %}
$ tar xvf OpenCV-2.4.0.tar
$ cd OpenCV-2.4.0
$ cmake -G "Unix Makefiles"
$ make
$ sudo make install
{% endhighlight %}


#### Installation Using Homebrew

I may have spoke too soon - If you plan on using the python bindings "python -c 'import cv2'" you may get a seg fault installing out of the box if you were using Python 2.6. To fix this issue, you need to install Python 2.7.* from the dmg download on Python.org. After it is installed you need to rebuild and re-install this project like above but after you run cmake, you need to modify the following variable PYTHON_LIBRARY:FILEPATH to

`/Library/Frameworks/Python.framework/Versions/2.7/lib/libpython2.7.dylib`

Than re-run `make/sudo make install`.

It is important to note that if you want to use the OpenCV's Python bindings `import cv` or `import cv2`, and you are using virtual environment (recommended), you need to copy the cv.so/cv2.so shared libaries into your `sites-packages` directory:


