---
layout: post
title:  "Using Django Signals With Many-To-Many relationships"
date:   2014-10-27
categories: django,postgres,rdbms
---

Sometimes it is useful to perform an action after something has been added to a many-to-many model in Django. The offical Django documentation is pretty sparse on this topic, so I figured I would document a decent solution. In the top of your `models.py` file, import Django signals.

Let says your models are setup as follows:

{% highlight python %}

from django.db import models

class Event(models.Model):
	name	= models.CharField(max_length=255, help_text="Name of the event")

class Contact(models.Model):
	full_name = models.CharField(max_length=255, 
			help_text="Full ame of the contact")
	events = models.ManyToManyField(Event, blank=True, null=True, 
		help_text="Events this contact is associated with")
	
{% endhighlight %}

And now lets say you want to perform some action, say send an email, everytime a new event is added to the contact.events attribute. The code that will do this looks like this:

{% highlight python %}

def m2m_send_email(signal, sender, **kwargs):
	"""
		When someone adds/removes someone from the guestlist, you need
		to create a RSVP receipt.
	"""
	from apps.invites.models import RSVPStatistics
	
	contact = kwargs['instance']
	action = kwargs['action']
	if kwargs['pk_set']:
		events = kwargs['model'].objects.filter(pk__in=kwargs['pk_set'])
		if action == 'post_add':
			# send email when contact was added
		elif action == 'post_remove':
			# send email when contact was removed
		else:
			pass


models.signals.m2m_changed.connect(m2m_send_email, Contact.events.through)


{% endhighlight %}

`m2m_send_email` will get called every time you call `contact.events.add(event); contact.save()`. You can decide which action you want to support and when to send the email by checking `kwargs['action']`.