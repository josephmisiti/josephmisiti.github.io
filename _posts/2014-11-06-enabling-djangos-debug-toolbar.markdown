---
layout: post
title:  "How To Enable Django-Debug-Toolbar"
date:   2014-11-06
categories: javascript
---

If you ever get to the point that you need to optimize your website, I recommend you check out the [Django-Debug-Toolbar](http://django-debug-toolbar.readthedocs.org/en/1.2.2/). It provides a pretty amazing set of features that can be used to trace slow pieces of code, investigate SQL queries, debug headers, requests, and even trace Django templates.

There are a lot of instructions around the web on how to get it up and running - but none of them seemed to work for me. Here is what I had to do:

First thing first, install the django debug toolbar using pip:

{% highlight python %}
pip install django-debug-toolbar
{% endhighlight %}

After the toolbar is installed, update your `urls.py` file to support the debug urls. 

{% highlight python %}
if settings.DEBUG:
	import debug_toolbar
	urlpatterns += patterns('',
		url(r'^__debug__/', include(debug_toolbar.urls)),
	)
{% endhighlight %}

Next, you need to add the specific options to your `settings.py` file. You can read more about the different options [here](http://django-debug-toolbar.readthedocs.org/en/1.2.2/configuration.html), but I just like to added them all:

{% highlight python %}
if DEBUG:
	INSTALLED_APPS += ( 'debug_toolbar',)
	MIDDLEWARE_CLASSES += ('debug_toolbar.middleware.DebugToolbarMiddleware',)
	DEBUG_TOOLBAR_PANELS = [
	    'debug_toolbar.panels.versions.VersionsPanel',
	    'debug_toolbar.panels.timer.TimerPanel',
	    'debug_toolbar.panels.settings.SettingsPanel',
	    'debug_toolbar.panels.headers.HeadersPanel',
	    'debug_toolbar.panels.request.RequestPanel',
	    'debug_toolbar.panels.sql.SQLPanel',
	    'debug_toolbar.panels.staticfiles.StaticFilesPanel',
	    'debug_toolbar.panels.templates.TemplatesPanel',
	    'debug_toolbar.panels.cache.CachePanel',
	    'debug_toolbar.panels.signals.SignalsPanel',
	    'debug_toolbar.panels.logging.LoggingPanel',
	    'debug_toolbar.panels.redirects.RedirectsPanel',
	]
	SHOW_TOOLBAR_CALLBACK = True
{% endhighlight %}

Make sure they're available in **DEBUG** mode only.

Finally, collect your static files to get the `css`, `js` and `html` that come with it package

{% highlight python %}
./manage.py collectstatic
{% endhighlight %}

You should now see the debug tool bar on the left side of your site.

Couple last notes:

* to run `collectstatic` you need to have the following variables set 

{% highlight python %}
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'
STATICFILES_DIRS = ()
{% endhighlight %}

* to view the html in the debug-tool-bar, you probably need the following code at the bottom of your main `urls.py` file

{% highlight python %}
if settings.DEBUG:
	urlpatterns += patterns('',
		(r'^media/(?P<path>.*)$', 'django.views.static.serve',
			{'document_root': settings.MEDIA_ROOT}),
	)
	urlpatterns += patterns('',
		(r'^static/(?P<path>.*)$', 'django.views.static.serve',
			{'document_root': settings.STATIC_ROOT}),
	)
{% endhighlight %}