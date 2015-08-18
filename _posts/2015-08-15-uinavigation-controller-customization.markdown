---
layout: post
title:  "Customizing UINavigation Controllers"
date:   2015-08-15
categories: ios
---

The following are some useful snippets to customize `UINavigationController`.

#### Applying Custom  Colors To NavigationBar

{% highlight objc %}

[self.navigationController.navigationBar setBarTintColor:[UIColor redColor]];

{% endhighlight %}

#### Removing The Bottom Boarder From UINavigationBar

{% highlight objc %}

[[UINavigationBar appearance] setBackgroundImage:[[UIImage alloc] init]
                                  forBarPosition:UIBarPositionAny
                                      barMetrics:UIBarMetricsDefault];

[[UINavigationBar appearance] setShadowImage:[[UIImage alloc] init]];

{% endhighlight %}

#### Add UISearchBar To UINavigationBar

{% highlight objc %}

_searchBar = [[UISearchBar alloc] initWithFrame:CGRectMake(-5.0, 0.0, 320.0, 44.0)];
_searchBar.autoresizingMask = UIViewAutoresizingFlexibleWidth;
_searchBar.searchBarStyle = UISearchBarStyleMinimal;
_searchBar.placeholder = @"";
_searchBar.delegate = self;

_searchController = [[UISearchDisplayController alloc]
                    initWithSearchBar:_searchBar
                    contentsController:self];

_searchController.delegate = (id)self;
_searchController.searchResultsDataSource = self;
_searchController.searchResultsDelegate = self;

self.navigationItem.titleView = _searchBar;

{% endhighlight %}