---
layout: post
title:  "Hiding the UISearchBar with animiation"
date:   2015-04-20
---

I recently built an application where I wanted to be able to toggle the `UISearchBar` that sits on top of the UITableView. Basically, the app would provide a magnifiying glasses button and when clicked, it would hide this view.
By default, the search bar would be showing.

The first step to this is to create a local `BOOL` named `_isHidden` and set it to `NO` in `viewDidLoad`. 


{% highlight objc %}

- (void)viewWillAppear:(BOOL)animated {
    _isHidden = NO;
	...
    tableViewHeader = self.tableView.tableHeaderView;
}

{% endhighlight %}



Now, to make the UISearch bar disappear, you need to use a UIView animation block:



{% highlight objc %}


- (void)toggleSearchBar{
    CGRect frame;
    if(!_isHidden){
        frame = CGRectMake(0, 0, 320, 0.0f);
        _isHidden = YES;
    } else {
        frame = CGRectMake(0, 0, 320, searchBarHeight);
        _isHidden = NO;
    }

    [UIView
     animateWithDuration:0.3
     animations:^{
         searchBar.frame  = frame;
     } completion:^(BOOL finished) {
         if(_isHidden){
             self.tableView.tableHeaderView = nil;
         } else{
             self.tableView.tableHeaderView = tableViewHeader;
         }
     }];
}

{% endhighlight %}


This code is pretty simple. First, I'm looking at the global `_isHidden` variable and setting the frame of the `UISearchBar` to either `0.0f` or `searchBarHeight` (currently set to `44.0f`). In the animation block, I am reseting the frame over a period of 300 milliseconds. After the animation is complete, it is important that you reset the tableHeaderView, otherwise you are going to see a gap at the top of your `UITableViewController`. To hide this gap, you set 

{% highlight objc %}

self.tableView.tableHeaderView = nil;

{% endhighlight %}

And when you want to un-hide the `UISearchBar`, use: 

{% highlight objc %}

self.tableView.tableHeaderView = tableViewHeader;

{% endhighlight %}


Notice, `tableViewHeader` was set in `viewDidLoad`. I can confirm this works pretty well, and I have not found a better solution online after searching for about 15 minutes.
