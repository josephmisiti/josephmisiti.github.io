---
layout: post
title:  "Replacing Django AuthUser with a Custom User Model"
date:   2014-07-24
categories: django,postgres
---


I recently attemped to replace Django's[ built-in user model](https://docs.djangoproject.com/en/dev/topics/auth/) (`auth_user` table) with a custom user model.
This tutorial explains how to add a custom user model in Django>=1.6 with migrations using Django South.

#### Introduction

Django comes with a build-in authentication system that makes adding users and authenicating them fairly
easy. To utilize Django authenication system, simply add the following three applications to your `INSTALLED_APPS` in
`settings.py` and you're good to go:

{% highlight python %}
INSTALLED_APPS = (
	'django.contrib.auth',
	'django.contrib.contenttypes',
	'django.contrib.sessions',
)
{% endhighlight %}

The problem with the built-in user model is it is not easily extendible. If you look around Github,
you can see multiple examples of different ways to add additional attributes to this model. The easiest
way seems to be creating a `Profile` model that reference the user model via a One-to-One relationship:

{% highlight python %}
class Profile(models.Model):
    user = models.OneToOneField(User, unique=True, related_name="profile")
	...
{% endhighlight %}

This method certainly works, but requires an extra database query. On top of that, since Django 1.6 
introduced the ability to easily add [custom user models](https://docs.djangoproject.com/en/dev/topics/auth/customizing/), if you are starting with a new project,
there is absolutely no reason not to use this protocol.

#### Creating A Custom User Model

The first step to creating a custom user model in Django>=1.6 is to define the model. I recommend
keeping all current [attributes](https://github.com/django/django/blob/master/django/contrib/auth/models.py) that are on the built-in user model. Here is an example of a custom
user model I built. It lives in my `apps.accounts.models` file.


{% highlight python %}
import re
from django.db import models
from django.core import validators
from django.utils.translation import ugettext_lazy as _
from django.utils import timezone
from django.contrib.auth.models import AbstractBaseUser
from django.contrib.auth.models import PermissionsMixin
from django.contrib.auth.models import UserManager
from model_utils import Choices


class User(AbstractBaseUser, PermissionsMixin):

	class Meta:
		app_label = 'accounts'
		db_table = "user"
		#ordering=["created"]

	username = models.CharField(_('username'), max_length=75, unique=True,
		help_text=_('Required. 30 characters or fewer. Letters, numbers and '
					'@/./+/-/_ characters'),
		validators=[
			validators.RegexValidator(re.compile('^[\w.@+-]+$'), 
			_('Enter a valid username.'), 'invalid')
		])
	full_name = models.CharField(_('full name'), max_length=254, blank=True)
	short_name = models.CharField(_('short name'), max_length=30, blank=True)
	email = models.EmailField(_('email address'), max_length=254, unique=True)
	is_staff = models.BooleanField(_('staff status'), default=False,
		help_text=_('Designates whether the user can log into this admin '
					'site.'))
	is_active = models.BooleanField(_('active'), default=True,
		help_text=_('Designates whether this user should be treated as '
					'active. Unselect this instead of deleting accounts.'))
	date_joined = models.DateTimeField(_('date joined'), default=timezone.now)

	objects = UserManager()

	USERNAME_FIELD = 'username'
	REQUIRED_FIELDS = ['email']
	
	def get_full_name(self):
		return self.full_name

	def get_short_name(self):
		return self.username

	def __unicode__(self):
		return self.email

	
	USER_TYPES = Choices('investor', 'issuer_sponsor', 
	'service_provider','broker_dealer')
	user_type = models.CharField(choices=USER_TYPES, max_length=50)

{% endhighlight %}


If you compare this model to what is built-in to Django, you can see that the custom user-model that I created
inherits from the same two Django mixins/base-classes (AbstractBaseUser, PermissionsMixin), and my **custom**
model contains one new proprietary attribute `user_type`

After you have defined the custom user model, you have to tell Django to swap user models from `auth_user` to use
your new user table called `user`. This can be done by setting the following attribute in `settings.py`:

{% highlight python %}
AUTH_USER_MODEL = 'accounts.User'
{% endhighlight %}

Once you have this in place, you need to create a the new tables for users/permissions. What you do here depends on
if this is a new app (no `auth_user` table) or existing app (pre-existing `auth_user` table).

If this is a new app, you are setting up your authenication system for the first time, then you should just be able to
run 

```
./manage.py syncdb 
```

And you are good to go. Your tables are created and Django never used the auth_user table because it picked up the 

{% highlight python %} AUTH_USER_MODEL {% endhighlight %} 

in your `settings.py` file.

If you have an existing application with a pre-existing auth_user. You are going to need to install the new table and then migrate your users over. I am not going to create an example of the migration script here, but it shouldnt be too hard.

To create a table, we are going to user [Django South](http://south.aeracode.org/). 

If this is the first time using South on the application

1. Initialize the app to use South

     ```./manage.py schemamigration accounts --initial```
	
1. Perform a fake migration on the user table

    ```./manage.py schemamigration accounts --auto```
	
1. Migrate

     ```./manage.py migrate```
	 
	 
If you already have migrations in the accounts folder:

1. Perform a fake migration on the user table

    ```./manage.py schemamigration accounts --auto```
	
1. Migrate

     ```./manage.py migrate```
	 
	 
Finally, fire up PSQL and make sure the table was created:

```
 public | south_migrationhistory        | table    | josephmisiti
 public | south_migrationhistory_id_seq | sequence | josephmisiti
 public | user                          | table    | josephmisiti
 public | user_groups                   | table    | josephmisiti
 public | user_groups_id_seq            | sequence | josephmisiti
 public | user_id_seq                   | sequence | josephmisiti
 public | user_user_permissions         | table    | josephmisiti
 public | user_user_permissions_id_seq  | sequence | josephmisiti
```
BOOM!! You now have a custom user model. Adding new attributes is as simple as creating
new South migrations and running migrate.


