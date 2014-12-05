---
layout: post
title:  "An Overview of iOS Properties and Methods"
date:   2014-12-04
---

Coming from a Python background, looking at iOS can seem daunting at first - the square brackets are very annoying (what the hell is a message anyways?), the compile-time errors can be very opaque, and there is a whole new group of reserved words in the `.m` and `.h` files.

I am going to briefly outline iOS properties and the meaning of the reserved words:

#### Strong vs. Weak References

Since you are dealing with pointers now, there is a new situation you have to avoid known as a [retain cycle](http://stackoverflow.com/questions/19892245/understanding-retain-cycle-in-depth). Since Apple introduced [Automatic Reference Counting (ARC)](https://www.google.com/search?q=apple+arc&oq=apple+arc&aqs=chrome..69i57j0j69i60l4.1118j0j4&sourceid=chrome&es_sm=91&ie=UTF-8), you do not have to worry about retain/release - the compiler will add that for you. What you do have to avoid is when two objects on the heap have **strong** references to each other (think of the implementation of a linked-list) - ARC will never clean up that memory and you will have a leak. To avoid that situation, you need to have one of the objects with a **strong** reference (the parent), and the other with a **weak** reference (the child). The object with the weak reference will then keep track of the parent and when it becomes unreferenced - it will self destruct.

It is worth noting that `strong` and `weak` should only be added to instance variables that are pointers. There is no need to use these for basic types like `int`, etc.

#### Atmoic vs Non-Atomic

These may be new key words for a rookie non-iOS programmers, but what you need to know is

1. Instance proporties are `atomic` by default (this is annoying)
2. Atomic should be used in threaded applications, `non-atomic` should be used in non-threaded applications
3. Most of the time, you want to use the key word `non-atomic`

For a really great explanation of the differences, please read this [StackOverflow answer](http://stackoverflow.com/a/589392/323578)

#### Synthesize

You will see the key work `synthesize` in the implementation files. This is Apples way of automatically creating getters and setters for an instance variable - in fact, you do not even need to create instance variables anymore because calling `synthesize` will add them for you

So for example, if in your header file

You may have to drop the existing table if it is already in your database.

{% highlight objective-c %}
@interface SomeClass : UIView

@property (strong, nonatomic) UILabel * titleLabel;

@end
{% endhighlight %}

And in your implementation file you have


{% highlight objective-c %}
@implementation INInboxNavTitleView

@synthsize titleLabel

@end
{% endhighlight %}

you can think of your implementation file is actually being the following:


{% highlight objective-c %}
@implementation INInboxNavTitleView

-(void)setTitleLabel:(UILabel*)label{
    self.titleLable = label;
}

-(UILabel*)getTitleLabel:(UILabel*){
    return self.titleLable;
}

@end
{% endhighlight %}

And dont worry, if you need to do something special in your getter or setter, simply define it in your implementation file and the compile will pick up your definition.

#### Copy

You wont see the `copy` attribute all the time, but in general, when you define a property that points to an instance of a class that has a mutable subclass, it is better (safer) to make a copy then point the object that could have other owners.

This is useful attribute when building third party libraries, where you are writing or using classes for other people to use. Since you dont know how they are going to use your class, you have to "program defensively" (thank you Big Nerd Ranch)
