---
layout: post
title:  "Sorting Boolean Columns In JQuery Datatables"
date:   2014-11-04
categories: javascript
---

Recently I had some data in a table and was using the excellent front-end library [jQuery Datatables](http://www.datatables.net/) to interact with the data. One of the columns was boolean and was not sorting correctly. I googled around the internet and hacked together a solution that worked for me.

First off, you have to add some hidden html to your table. In my case, I was storing the booleans as an interactive column of buttons the user could toggle with an `onclick` event. So I was starting off with some HTML that looked like this for `true` columns:

{% highlight html %}
	<td data-action="add" class="js-action icon-checkmark bkg green icons">
	</td>
{% endhighlight %}

And looked like this for `false` columns:

{% highlight html %}
	<td data-action="add" class="js-action icon-checkmark incomplete icons tooltip_html" >
	</td>
{% endhighlight %}

Basically just a red/green button depending on the state, pretty amazing shit I know. So I first added two hidden `div` tags in each of these table data tags using an html data tag to specify if it was `true` or `false` using zero or one.

{% highlight html %}
	<td data-action="add" class="js-action icon-checkmark bkg green icons">
		<div style="display:hidden"data-sort="1"></div>
	</td>
{% endhighlight %}

And looked like this for `false` columns:

{% highlight html %}
	<td data-action="add" class="js-action icon-checkmark incomplete icons tooltip_html" >
		<div style="display:hidden" data-sort="0"></div>
	</td>
{% endhighlight %}

For all you beginners out there, pay attention to the `data-sort` attribute.

Next, I needed to "dive" into some javascript and tell datatables how to sort this data:


{% highlight javascript %}

jQuery.fn.dataTableExt.oSort['checked-in-asc']  = function(a,b) {
	var a_sort = parseInt($(a).data("sort"));
	var b_sort =  parseInt($(b).data("sort"));
	return ((a_sort < b_sort) ? -1 : ((a_sort > b_sort) ?  1 : 0));
};

jQuery.fn.dataTableExt.oSort['checked-in-desc'] = function(a,b) {
	var a_sort = parseInt($(a).data("sort"));
	var b_sort =  parseInt($(b).data("sort"));
	return ((a_sort < b_sort) ?  1 : ((a_sort > b_sort) ? -1 : 0));
};
	
{% endhighlight %}

Here I'm simply pulling the value out of the `data-sort` attribute, converting it to an integer, and then doing a simple comparision for both ascending and decending sort.

I needed to tell the datatable what type of column I was using.

{% highlight javascript %}

$('.js-dt').dataTable({
	"aoColumns": [
				null,
				null,
				null,
				null,
				{ "sType": "checked-in" },
				null
			],
	aaSorting: [[4, "desc"]],	
	iDisplayLength: 10,
	"sDom": '<"clear">lfrtip<"clear">',
	"bDestroy": true
});
{% endhighlight %}

In my case, my new column was living in column 5 in my table. Finally, I forced the table to sort in decending order by default, refreshed, and boom, I am now sorting booleans.

I hate javascript, but if you like articles like this, follow me on twitter [@josephmisiti](https://twitter.com/josephmisiti)