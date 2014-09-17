---
layout: post
title:  "How to rename a Postgres table in Django Using South"
date:   2014-09-16
categories: django,postgres
---

I recently started a project and used a custom user model, but unfortunately named the table `user` instead of the default user table that comes with Django `auth_user`. In the following examples, my user model lives in `apps.accounts.models.User`

I decided to use South to rename the table, it wasnt really that hard but here are some instructions on how to do it:

First you need to create an empty schema migration in the django app that has your custom user model (or really any model you want to rename):

```bash
./manage.py schemamigration accounts rename_user_to_auth_user --empty
```

To rename the table you have to update the schema migration create to contain the following Python code:

{% highlight python %}
class Migration(SchemaMigration):

	def forwards(self, orm):
		db.rename_table('user', 'auth_user')


	def backwards(self, orm):
		db.rename_table('auth_user','user')  

{% endhighlight %}

Now run `./manage.py migrate` and you have a new table. If you are updating the Django users table, you actually have to add yet another migration to rename the `user_groups` and `user_permission` tables:


{% highlight python %}
class Migration(SchemaMigration):

    def forwards(self, orm):
        db.rename_table('user_groups', 'auth_user_groups')
        db.rename_table('user_user_permissions', 'auth_user_user_permissions')

    def backwards(self, orm):
        raise "Don't go backwards."

{% endhighlight %}
