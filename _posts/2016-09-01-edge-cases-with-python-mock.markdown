---
layout: post
title:  'Mocking Module Level Functions In Python Unit Tests'
date:   2016-09-01
categories: python,mock
---

If you write a lot of unit tests in python, especially on complex pieces of software, no doubt you have come
across python's [mock module](https://pypi.python.org/pypi/mock). As of writing this, I just noticed that it has now
been pulled into the python standard library [unittest module](https://docs.python.org/dev/library/unittest.mock.html)

It is easy to use mock to mock class-level instance methods:

{% highlight python %}

class ClassA(object):

    def hello(self):
        return "Hello"
    
{% endhighlight %}

And now in some unit test

{% highlight python %}

class Test(TestCase):
    
    @mock.patch('classA.hello', mock.Mock(return_value="world"))
    def test_class(self):
        cls = ClassA()
        cls.hello()
            
{% endhighlight %}

That was easy enough, but mocking module level functions is actually not as easy. Lets say I have some module called `name_utis.py` that's sole job is to concatenate first name and last name:

{% highlight python %}

// name_utils.py

def make_whole_name(first, last):
    return "{} {}"format(first, last)

{% endhighlight %}


And lets say this function is called by some other class:

{% highlight python %}

class ClassB(object):
    
    def __init__(self, first, last):
        self.first = first
        self.last = last

    def get_name(self):
        return make_whole_name(self.first, self.last)
        
{% endhighlight %}

My goal is to mock `make_whole_name`, not `get_name`. This can be done by using the following

{% highlight python %}

class Test(TestCase):
    
    @mock.patch('path.to.make_whole_name.make_whole_name')
    def test_class(self, first_name, last_name):
        
        dhs.first_name = 'joseph'
        dhs.last_name = 'misiti'
        
        cls = ClassB('hello','world')
        self.assertEquals(cls.get_name(), 'joseph misiti')
            
{% endhighlight %}


