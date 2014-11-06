---
layout: post
title:  "Sorting Backbone Collections By Attributes Other Than Model ID"
date:   2014-11-05
categories: javascript
---

By default, `Backbone.Collection` is sorted by model id (usually from the database). If you want to sort by another attribute, you need to define a `comparator` function. Here is an example that words for a `boolean` value

{% highlight javascript %}

var Tags = Backbone.Collection.extend({

	sort_key: 'id', // default sort key

	comparator: function(item) {
		return !item.get(this.sort_key);
	},
	
	sortByField: function(fieldName) {
		this.sort_key = fieldName;
		this.sort();
	},

	model: FETCHER.Models.TagModel,

	url: '/api/v1/tags/',

});

{% endhighlight %}

After adding code like this to your collection, you can simply make a simple call like 

{% highlight javascript %}
FETCHER.Collections.Tags.sort("attribute")
{% endhighlight %}

And if `attribute` is boolean, the trues fill float to the top of the collection. Also, note that youc an re-render elements in your UI by listening to the `sort` call:

{% highlight javascript %}
this.listenTo(FETCHER.Collections.Tags,'sort',this.render,this);
{% endhighlight %}

