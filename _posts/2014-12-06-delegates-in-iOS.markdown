---
layout: post
title:  "Delegation in iOS"
date:   2014-12-06
---

Delegates in iOS are basically object oriented versions of callbacks. Whereas a callback is a function which is defined before an event, and is called every time time an event occurs, a delegate is an object that acts on behalf of, or in coordination with, another object when that object encounters an event in a program.

The easiest way to explain this is to look at a simple example in `javascript` and in then in `objective C`:

Lets say you want to write a **very** simple key logger, which dumps the key board characters pressed to standard out.

In javascript, this can be done very easily utilizing jQuery:

{% highlight javascript %}
$("body").on("keyup", function(e){
    console.log(e.which)
})
{% endhighlight %}

In this example, the callback `function(e)` will be called anytime a keyboard key is pressed. (Yes, I know it only works if you are in a browser)

In objective C, the same thing can be accomplished through delegation:

{% highlight objc %}
#import <UIKit/UIKit.h>

@interface SomeClass : UIViewController <UITextViewDelegate, UITextFieldDelegate>

@property (nonatomic, strong, readonly) SAMTextField* someTextField;

@end
{% endhighlight %}

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


If you look at the creation of the text field, on line 12 we are selling our ViewController class `SomeClass` to be the delegate of the text field
