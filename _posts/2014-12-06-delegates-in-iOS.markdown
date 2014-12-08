---
layout: post
title:  "Delegation in iOS"
date:   2014-12-06
---

If you are new to iOS programming, one design pattern that you are going to need to quickly learn is [delegation](http://programmers.stackexchange.com/questions/190359/what-is-delegation-and-why-is-it-important-in-ios-programming): Delegates are an object oriented versions of callbacks (or that is how I like to think about them anyways). Whereas a callback is a function which is defined before an event, and is called every time an event occurs, a delegate is an object that acts on behalf of, or in coordination with, another object when that object encounters an event in a program.

The easiest way to explain this is to look at a simple example in `javascript` and in then in `objective-c`:

Lets say you want to write a **very** simple key logger, which dumps any key strokes to standard out.

In `javascript`, this can be done very easily utilizing jQuery:

{% highlight javascript %}
$("body").on("keyup", function(e){
    console.log(e.which)
})
{% endhighlight %}

In this example, the callback `function(e)` will be called anytime a keyboard key is pressed. (Yes, I know it only works if you are in a browser, but I am trying to write a trival example)

In objective C, the same thing can be accomplished through delegation:

{% highlight objc %}
#import <UIKit/UIKit.h>

@interface SomeClass : UIViewController <UITextViewDelegate, UITextFieldDelegate>

@property (nonatomic, strong, readonly) SAMTextField* someTextField;

@end
{% endhighlight %}

The first thing to notice here is SomeClass is conforming to the `UITextFieldDelegate` and
`UITextViewDelegate` protocols. Also notice this view contains a single text field, a [SAMTextField](https://github.com/soffes/SAMTextField) to be exact.


{% highlight objc linenos %}

@implementation SomeClass

-(SAMTextField*) firstName {
    if(!_someTextField){
        _someTextField = [[SAMTextField alloc]
        initWithFrame:CGRectMake(10.0f, 00.0f, [[self class] textFieldWidth], 70.0f)];
        _someTextField.keyboardType = UIKeyboardTypeEmailAddress;
        _someTextField.autocapitalizationType = UITextAutocapitalizationTypeNone;
        _someTextField.autocorrectionType = UITextAutocorrectionTypeNo;
        _someTextField.contentVerticalAlignment = UIControlContentVerticalAlignmentCenter;
        _someTextField.returnKeyType = UIReturnKeyNext;
        _someTextField.delegate = self;
        _someTextField.placeholder = @"First Name";
        _someTextField.borderStyle=UITextBorderStyleNone;
    }
    return _someTextField;
}

-(BOOL)textField:(UITextField *)textField
shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string
{
    if(textField == self.firstName ){
        NSString* currentText = self.firstName.text;
        NSLog(@"%@",self.someTextField.text)
    }
    return YES;
}

@end

{% endhighlight %}

Now let's see how delegation works. When I am defining the text field, you will see on `line 12` that I am setting my class `SomeClass` to be the delegate of the text field. This is me telling SAMTextField to send any protocol message from `UITextFieldDelegate` and `UITextViewDelegate` to the instance of `SomeClass`.

Finally, I define instance methods of any of the protocol messages I want to receive data from. In this case, I define `shouldChangeCharactersInRange`, which is a part of [UITextFieldDelete](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITextFieldDelegate_Protocol/index.html)

From the documentation:

    The text field calls this method whenever the user types a new
    character in the text field or deletes an existing character.
