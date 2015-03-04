---
layout: post
title:  "Getting Started With Django On Heroku"
date:   2015-03-03
---

Here is a set of instructions on how to get a Django app running on Heroku. This assumes
you have a repo in github already.

First, create a new app in Heroku and connect the repo to github:

{% highlight python %}
heroku login
heroku git:remote -a <APP_NAME>
{% endhighlight %}

Next, you can attach a Postgres database for free executing the following command:

{% highlight python %}
heroku addons:add heroku-postgresql
{% endhighlight %}

Next, you need to go back to the Heroku Admin Panel And [Create A Database](https://postgres.heroku.com/databases). I am using the dev version so my database
is still free.

To tell django about your database, I recommend using the `dj-database-url` package. This can be done by inserting the following lines of code into you `settings.py` file.

{% highlight python %}
import dj_database_url
DATABASES={}
DATABASES['default'] =  dj_database_url.config()
{% endhighlight %}


## Tailing Heroku Log Files

To tail the current Heroku log files of your app, execute the following command

{% highlight python %}
heroku logs -t 
{% endhighlight %}

## Executing A Django Command

To execute a Django `manage.py` command, you can execute the following command

{% highlight python %}
heroku run python manage.py syncdb
{% endhighlight %}


## Getting to A Bash Shell

You can get to a bash shell pretty easily by executing the following command:

{% highlight python %}
heroku run bash
{% endhighlight %}

And you can get to a Python REPL with Django settings by executing the following command:

{% highlight python %}
heroku run python manage.py shell
{% endhighlight %}


## Copying a File

{% highlight python %}
heroku run 'cp some_file copied_file'
{% endhighlight %}



