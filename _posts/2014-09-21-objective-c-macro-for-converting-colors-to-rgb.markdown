---
layout: post
title:  "Using Hex Colors In Objective C"
date:   2014-09-21
categories: objectivec
---


If you are coming from HTML/CSS and trying to design for the iPhone/iPad, one thing that you are going to want to do is define colors in [Objective C](http://en.wikipedia.org/wiki/Objective-C) using hex values from CSS. Here is the [marco](http://en.wikipedia.org/wiki/Macro_(computer_science)) that can be used to do that.

{% highlight c++ %}
    #define UIColorFromRGB(rgbValue) [UIColor \
            colorWithRed:((float)((rgbValue & 0xFF0000) >> 16))/255.0 \
            green:((float)((rgbValue & 0xFF00) >> 8))/255.0 \
            blue:((float)(rgbValue & 0xFF))/255.0 alpha:1.0]

{% endhighlight %}

Simply add the macro to your [PCH](http://en.wikipedia.org/wiki/Precompiled_header) file and then you can start using hexidecimal colors immediately as follows

{% highlight c %}

UITextField* _stateField = [[UITextField alloc] initWithFrame:CGRectMake(0.0f,10.0f,100.0f,100.0f)]
 _stateField.textColor = UIColorFromRGB(0x488abb);

{% endhighlight %}