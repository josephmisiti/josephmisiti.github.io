---
layout: post
title:  "Dirty Fields In Django"
date:   2014-11-09
categories: python,django
---

I recently had a software requirement in which I needed to send a notification when a model attribute changed. I looked a few [third](https://github.com/smn/django-dirtyfields) party projects, but they either did not do what I need or were not maintained, so I decided to role my own. Here is the solution, I came up with, via a Mixin:

{% highlight python %}

import datetime

class DirtyMixin(object):
@property
def is_dirty(self):
	dirty_fields = []
	is_dirty = False
	if self.pk:
		db_obj = self.__class__.objects.get(id=self.pk)
		for f in self._meta.local_fields:
			if(self.__getattribute__(f.name),datetime):	
				try:
					attribute = self.__getattribute__(f.name)
					if attribute != db_obj.__getattribute__(f.name):
						dirty_fields.append(f.name)
						is_dirty = True
				except TypeError:
					pass
								
	return is_dirty, dirty_fields
		
{% endhighlight %}

If you want to use this, you can added a django signal to fire on `Model.pre_save` and you would add some code like this:

{% highlight python %}
@staticmethod
def pre_save(sender, **kwargs):	
	from apps.accounts.models import User 
		
	instance = kwargs.get('instance')
	created = kwargs.get('created')


	is_dirty, dirty_fields = instance.is_dirty
	if is_dirty:
		# blah blah blah
...

models.signals.pre_save.connect(YOURMODEL.pre_save, sender=YOURMODEL)
{% endhighlight %}

If any model field has changed, then `is_dirty` will return `True` and `dirty_fields` will contain the field names of the fields that were updated.