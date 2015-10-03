---
layout: post
title:  "Getting Started With Amazon Redshift"
date:   2015-09-28
categories: etl,redshift,amazon,devops
---

I recently worked on a project in which I was asked to migrate someones analytics database into a Redshift cluster. The backend of this website was using Django, so all of my development was done in Python.

A had a couple of goals before starting the project:

1. Keep everything in Python
1. Utilize Django's ORM when possible
1. Make migrations easy

The first step is to launch a Red Shift cluster on EC2 and your local ip address has to be added CIDR/IP security group of in the redshift dashboard. For testing I like to set it to `0.0.0.0\0`.

Once you have your cluster up and running, you can try to connect to it using the following command

```
psql "host=XXXXXXXX.cilmdlzscpk4.us-east-1.redshift.amazonaws.com user=XXXXXXX dbname=XXXXX port=5439"
``` 

If you can successfully connected, you're all set, you need to setup your tables. I used the `psycopg2` to setup these tables, but you could of course manually enter them into the REPL. If you want to take my approach first, assuming you are running on ubuntu, install the `postgres` requirements

```
sudo apt-get install -y postgresql-client-9.3 libpq-dev
```

Now install `psycopg2` using pip:

```
pip install psycopg2
```

Since we are going to utilize `django's` orm we need to update `settings.py`:

{% highlight python %}
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'home',
        'USER': 'home',
        'PASSWORD': 'home',
        'HOST': 'localhost',
    },
    'analytics': {
        'NAME': 'redshift',
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'USER': 'redshift',
        'PASSWORD': 'xxxxxxxxx',
        'HOST': 'XXXXXXXXXXX.cilmdlzscpk4.us-east-1.redshift.amazonaws.com',
        'PORT': 5439,
    }
}
{% endhighlight %}

We also need to use a database router, to make sure that reads and writes go to redshift on a per app basis (this is something I did per the design of my application, but you might not need to do this):

{% highlight python %}
class Router(object):

    def db_for_read(self, model, **hints):
        if model._meta.app_label == 'analytics':
            return 'analytics'
        return None

    def db_for_write(self, model, **hints):
        if model._meta.app_label == 'analytics':
            return 'analytics'
        return None

    def allow_relation(self, obj1, obj2, **hints):
        if obj1._meta.app_label == 'analytics' or \
           obj2._meta.app_label == 'analytics':
           return True
        return None

    def allow_migrate(self, db, app_label, model=None, **hints):
        if app_label == 'analytics':
            return False
        return None
        
{% endhighlight %}

And finally, add the router to `settings.py`

{% highlight python %}

DATABASE_ROUTERS = ['path.to.router.Router',]

{% endhighlight %}


OK. everything is setup and ready to go! Adding tables is as simple as calling SQLs `create table` command:

{% highlight python %}
CREATE_TABLE = """
    create table mytable(
        id INT IDENTITY(1,1),
        event_id integer not null,
        job_id integer not null,
        count integer not null,
        primary key(id));
"""
{% endhighlight %}

Now using `psycopg2`:

{% highlight python %}
conn = psycopg2.connect(
    host=settings.DATABASES['redshift']['HOST'], 
    user=settings.DATABASES['redshift']['USER'], 
    port=settings.DATABASES['redshift']['PORT'], 
    password=settings.DATABASES['redshift']['PASSWORD'], 
    dbname=settings.DATABASES['redshift']['NAME'])
cur = self.conn.cursor()
cur.execute(CREATE_TABLE)
conn.commit()
{% endhighlight %}


