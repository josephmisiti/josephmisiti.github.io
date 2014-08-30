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

The first step to setting up Typeahead is to download the single, minified js file and adding it your assets. After it has been added to your project, you need to setup an API call that will return the search results. I am currently using Django Tastypie for most of my APIs, create my search API endpoint there:

{% highlight python %}
	def search_venues(self, request, **kwargs):
		self.method_check(request, allowed=['get'])	
		results = {}
		results['venues'] = []
		
		query = request.REQUEST.get("q")
		if not query:
			venues = []
		else:
			venues = EventVenue.objects.filter(Q(name__istartswith=query) 
			| Q(space_name__istartswith=query))
			
		for venue in venues:
			results['venues'].append({
				'id' : venue.id,
				'name' : venue.name,
				'space_name' : venue.space_name,
				'address' : venue.address,
				'city' : venue.city,
				'state' : venue.state,
				'zipcode' : venue.zipcode,
			})
		return self.create_response(request, results)
{% endhighlight%}


After the API endpoint has been defined and tested, it is pretty easy to setup Typeahead. Here is a snippet of
code I used to point Typeahead at the new API endpoint:

{% highlight javascript %}
GLOBALS.Models.venue_auto_complete = new Bloodhound({
    
	datumTokenizer: function (datum) {
        return Bloodhound.tokenizers.whitespace(datum.value);
    },
	
    queryTokenizer: Bloodhound.tokenizers.whitespace,

    remote: {
        url: '/api/v1/venue/search/?q=%QUERY&format=json',
        filter: function (results) {
            return $.map(results.venues, function (venue) {
                return {
                    name : venue.name,
					space_name :  venue.space_name,
					address : venue.address,
					id : venue.id,
                };
            });
        }
    }
});

GLOBALS.Models.venue_auto_complete.initialize();
{% endhighlight%}

I also setup a global dictionary to pass to the Typeahead constructor:

{% highlight javascript %}
GLOBALS.Globals['auto_complete_options'] = {
	
	name: 'products',
	
	displayKey: 'space_name',
	
	source: GLOBALS.Models.venue_auto_complete.ttAdapter(),
	
	restrictInputToDatum: true,
	
	templates: {
		suggestion: function(context) { 
			var html = '<div class="js-suggestion">'
			html =  html + "<div class='js-space-name'>" + context.space_name + "</div>";
			html =  html + "<div class='js-name'>" + context.name + "</div>";
			html =  html + "<div class='js-address'>" + context.address + "</div>";
			html =  html + "<div>"
			return html; 
		}
	}
}
{% endhighlight%}

The one issue I ran into is Typeahead does not provide the ability out of the box to display the name of the venue but then store the id of the venue to send the server. The `displayKey` attribute controls which dropdown is selected and shows up in the box, but this doesnt not work correctly. I am actually really surprised that this is the default setting because I would imagine almost everyone would want the selected item from the dropdown to display a name, but store an database id. Regardless,  this snippet of code will fix the issue:

{% highlight javascript %}
$("document").ready(function(){

	$("#js-search-venue").typeahead(null, GLOBALS.Globals['auto_complete_options']);
	$("#js-search-venue").on('typeahead:selected', function (e, datum) {   
		$("input[name=venue_pk]").val(datum.id)
	});
});
{% endhighlight%}

My solution to storing the id involves adding a hidden input `<input name="venue_pk" type="hidden">`, and then adding a callback that listens to the selected event in the dropdown list. When an item is selected, I add the selected id to the hidden input and then an able to send it to the server in an HTTP POST call for saving in the database.

Anyways, this solution works for me, although it seems like a hack. If anyone else knows of a better way to do this, please let me know. There are multiple github issues [I](http://stackoverflow.com/questions/12389948/twitter-bootstrap-typeahead-id-label) [have](https://github.com/twitter/typeahead.js/issues/193) [found](http://stackoverflow.com/questions/20426718/bootstrap-typeahead-add-value-to-hidden-field) dealing with this issue, but this seemed like the simpliest solution to me.


