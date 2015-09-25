---
layout: post
title:  "Making Neural Art"
date:   2015-09-27
categories: deeplearning,machineleanring
---

A few weeks ago the blogosphere [1] went kinda crazy when [A Neural Algorithm of Artistic Style](http://arxiv.org/pdf/1508.06576v1.pdf) was published. I have to admit, I thought the results were pretty impressive and was looking forward to playing with the algorithm myself. All I needed was 

1. [Someone](https://github.com/andersbll) to implement the algorithm for me (too busy and lazy to do it myself)
1. A GPU
1. Some free time

The following are some notes I took on how to get started. I hope they help.

#### The Setup

I started with launching an GPU instance, pre-installed with CUDA, etc. on Amazon's US-East data center (ami-55deaf30). I actually initially tried to install the software myself, but was unable so I moved on. I have a separate repo for my installation work [here](https://github.com/josephmisiti/deep-learning-scripts). 

After your instance is running, login and you need to start installing software. First we need to install [cudarray](https://github.com/andersbll/cudarray).

```
git clone https://github.com/andersbll/cudarray
cd cudarray
```

I also followed the directions and set the following variables up before building anything (add these to your bashrc and do not forget to call `source` before building)

```
export CUDNN_ENABLED=1
export INSTALL_PREFIX=/usr/local/
export CUDA_PREFIX=/usr/local/cuda-7.5/
```

Now it is time to build the library:

```
make; 
sudo make install; 
```

Assuming you do not have any issues with building the library, you can now install the python bindings:

```
sudo python setup.py install
```

Next, we need to install the [deeppy](https://github.com/andersbll/deeppy) library.

```
git clone https://github.com/andersbll/deeppy
cd deeppy
sudo python setup.py install
```

I had no problem at this point but when I ran the command the software could not find the CUDA libraries:

```
ubuntu@ip-172-31-16-25:~/neural_artistic_style$ python neural_artistic_style.py --subject images/tuebingen.jpg --style images/starry_night.jpg
/usr/lib/python2.7/dist-packages/pkg_resources.py:1031: UserWarning: /home/ubuntu/.python-eggs is writable by group/others and vulnerable to attack when used with get_resource_filename. Consider a more secure location (set with .set_extraction_path or the PYTHON_EGG_CACHE environment variable).
  warnings.warn(msg, UserWarning)
CUDArray: CUDA back-end not available, using NumPy.
```

[Fixing this](https://github.com/andersbll/neural_artistic_style/issues/20) was as simple as setting the `LD_LIBRARY_PATH` so the software could find the appropriate shared libarires:

```
export LD_LIBRARY_PATH='/home/ubuntu/cudarray/build/;/usr/local/cuda-7.5/targets/x86_64-linux/lib/'
```

That is it, I was able to utlize the library and make neural art:


![input]({{ baseurl.url }}/images/posts/NN_input.png)

![output]({{ baseurl.url }}/images/posts/NN_output.png)

![output]({{ baseurl.url }}/images/posts/NN_output_1.png)

![output]({{ baseurl.url }}/images/posts/NN_output_2.png)

![output]({{ baseurl.url }}/images/posts/NN_output_3.png)


[1] http://www.dailymail.co.uk/sciencetech/article-3214634/The-algorithm-learn-copy-artist-Neural-network-recreate-snaps-style-Van-Gogh-Picasso.html
