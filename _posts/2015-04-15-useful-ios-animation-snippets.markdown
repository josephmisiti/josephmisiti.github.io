---
layout: post
title:  "Useful iOS View Animation Snippets"
date:   2015-04-15
---

The following gives a nice linaer transition from one frame location to another frame location:

{% highlight objc %}
    dispatch_async(dispatch_get_main_queue(), ^{
        [UIView beginAnimations:nil context:nil];
        [UIView setAnimationDuration:0.5];
        [UIView setAnimationCurve:UIViewAnimationCurveLinear];
        _bottomAnimatedBar.frame  = frame;
        [UIView commitAnimations];
    });
{% endhighlight %}

This also gives a nice linear transition, although it can be a little bumping depending on the coordinates of the frame:

{% highlight objc %}
    [UIView
     animateWithDuration:0.5
     animations:^{
         _bottomAnimatedBar.frame  = frame;
     }];
{% endhighlight %}

