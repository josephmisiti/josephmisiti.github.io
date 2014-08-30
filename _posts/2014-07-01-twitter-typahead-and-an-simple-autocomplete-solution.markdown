---
layout: post
title:  "Twitter's Typeahead And A Simple Autocomplete Solution"
date:   2014-07-01
categories: typeahead,javascript,twitter,github
---

I recently had to implement an auto-complete feature on a website I was building. The website
uses [select2]() for all selects/inputs and select2 does have any auto-complete feature that works
well except it appears to be impossible to pre-load the input with a selected, saved value.

After wasting many hours trying to get `initSelection` to fire, with not success, I moved on and decide to
use Twitter's autocomplete solution known as [Typeahead]()

{% highlight javascript %}

 initSelection: function(element, callback) {
        // the input tag has a value attribute preloaded that points to a preselected movie's id
        // this function resolves that id attribute to an object that select2 can render
        // using its formatResult renderer - that way the movie name is shown preselected
        var id=$(element).val();
        if (id!=="") {
            $.ajax("http://api.rottentomatoes.com/api/public/v1.0/movies/"
			+ id +".json", {
                data: {
                    apikey: "ju6z9mjyajq2djue3gbvv26t"
                },
                dataType: "jsonp"
            }).done(function(data) { callback(data); });
        }
    },

{% endhighlight%}

I finally did get Typeahead to work properly to close the feature, but it certainly was not simple. In case any other people are trying to do the same thing, I have attempted to document the easiet way to integrate a auto-complete solution using this library.