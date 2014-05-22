---
layout: post
title:  "11 Things I Wish I Knew About Django Development Before I Started My Company"
date:   2013-11-24
categories: django
---

I started [SocialQ](https://www.socialq.com) just about two years ago. Before I started the company, I had almost zero web development experience (I’m a data guy) - I started a company learning HTTP, Javascript, AJAX, and Django MVC from scratch. It’s been a wild ride, and our technology stack has since matured to using interesting technologies such as D3.js, Backbone.js, Celery, Mongo, Redis, and a bunch of other stuff - but it didnt happen over night. Looking at the thousands of lines of Django code everyday, I thought it would be worth pointing out things I wish I did differently:

1.  **Start off with the right directory structure:** 
    
    Starting off, I looked at a few different open-source projects for guidence ([1] and [2]), read a few blogs, but never had good idea on the best way to setup a Django project. Here is what I am currently using:

    The apps directory stores all of your customized django apps and the vendor directory stores any apps you do not want to install (or can’t install) using pip or easy_install. The bin directory stores all bash scripts that help you automate your development. I have scripts in here that deploy to staging & production servers, clean up directories, compress assets, backup databases, start/stop celery (locally), etc. The config directory stores all of your configuration files for databases, webservers, munin, celery, supervisor, etc. The media directory stores all static assets such as javascript, css, images, fonts, etc.The template directory stores all the html templates that make your site beautiful. Finally, the static directory is where your compressed assets get dumped into for production. I even created a template for you on github,go star it!

2. **Use Celery for asynchronous tasks AND cron jobs (no need to use unix crontab):** 

    The first two weeks of development you might not need to shove something into an asynchronous process, but when your non-technical co-founder starts asking why the site is hanging, it’s time to bust out celery (there are other options if you need something more lightweight). Any call that doesn’t need to be synchronous can be queued up and eventually consumed by a celery worker. I recommend using redis as a celery backend (see below) - do not waste your time with RabbitMQ unless you have a good reason to. I use celery for all sorts of tasks from sending out emails to pulling data from the Facebook API. Another intersting use of celery is you can setup periodic tasks to act as a cronjob. When I first started developing SocialQ, I was using UNIX crontab but have since moved everything into celery.

3. **Use Gunicorn instead of Apache for your webserver:** 

    I know - Apache is battle-tested and all (plus it got a lot of love in The World Is Flat), but two years ago, when I didnt know shit, it was pretty complicated to setup. I just wanted to get a website up and running and forgot about a print statement in my code - whoops— that took down my site with a 500. Apache has a large set configurations that are time consuming to understand.Gunicorn is very simple and gets the job done. Huge sites are using it too, at scale, so if you just created the next Instagram, you know you’re servers are not going to fail you[1]

    Update: This assumes NGINX is managing all incoming requests and serving static content.

4. **Don’t be afraid of using MongoDB as your primary data store:** 

    There is a lot of hate floating around Hacker News when someone starts talking about MongoDB. I am not gonna sit here and act like it’s the pancea for all web storage problems, but I will tell you what it is good for - fast iteration. South does a nice job with RDBMS migrations, but migrations seem a bit easier in Mongo (see $set and $unset). Two years ago, things were different and Mongo was much less mature — but since then 10gen has added the aggregation framework, full-text search, collection-level locking, etc. I am glad I started with Mongo and it will continue to be my primary datastore. Mongoengine works great with Django, and if you need more control, you can jump down into pymongo. A lot of other big companies love MongoDB too:

5. **Use named URLs, reverse, and the url template tag:** 

    This one seems dumb, I know, but man - I wish I knew about reverse when I started. Do yourself a favor and name all of your urls, and only refer to them by name in both templates using the url template tag and using reverse in the backend. It will potentially save you a lot of time in the future because nothing will be hard-coded and one url change will not break the site and unit tests.

6. **Get your settings.py file right:** 

    There has been a lot of discussion about what is the right approach to Django settings. You can read about these discussions here and here. Personally, I like adding local_settings.py to my top-level directory and then importing it at the bottom of my settings file, overriding any variables declared above it in settings.py. My method works well for me, but you should definitely figure out how do separate development, staging, and production level settings.

7. **Use supervisor for process monitoring:** 

    If you havnt heard of supervisor yet, and you are deploying to a unix-based machine, go here and start reading. Supervisor will control all of your processes for you. You just need to add a separate configuration file for each process. If the process goes down, supervisor will bring it back up. Examples of supervisor processes: Celery-beat, celery, gunicorn, statds, mongodb etc.

8. **Pick the right AJAX/JSON mechanism:** 

    Unless you want to do a full page reload with every HTTP request (which is completely ok, but SO 1995) you are going to want to send some data to the server using AJAX. The problem you will quickly hit is Django does not have a built in JSON HTTP response, so you are going to have to either man up and roll your own (good luck) or copy someone else smarter than you (this is what I did). Here is a decorator that works great, and this response is superb also:

9. **Use Redis - because it will eventually be your best friend:** 

    As I suggested above, at the beginning, Redis can just be used to queue your celery jobs. Later on, when the time comes you can store your sessions to redis. Then, you can use redis as a cache. Then you can use redis for auto-completion. Then …. I rest my case - just use Redis!

10. **Use munin and statds for process monitoring.** 

    Munin lets you make nice graphs of almost anything, statds lets you time or count (increment) anything. Add these to your project as soon as possible and monitor everything. You can easily write your own munin plugins in Python to monitor just about everything.

11. **Use jammit for static asset compression.** 

    Jammit came out of documentcloud, and even though it was built for Ruby on Rails you can do something like this and use it for Django pretty easily. One of the things I like about it is you can setup different named configurations for javascript and css for different sections of your site (dashboard, non-authenticated, standard). It supports lots of other stuff too.

Two years later, two years wiser, and many customers and mess-ups later, that is what I wish I would have done differently. If you are interested in content like this, please follow me at [@josephmisiti](http://www.twitter.com/josephmisiti).

[1. What Powers Instagram](http://instagram-engineering.tumblr.com/post/13649370142/what-powers-instagram-hundreds-of-instances-dozens-of)