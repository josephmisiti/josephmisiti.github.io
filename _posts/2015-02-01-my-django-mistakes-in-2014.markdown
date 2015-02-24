---
layout: post
title:  "My Django Mistakes in 2014"
date:   2015-02-01
categories: django,python
---

With 2015 rapidly approaching, I took some time to think about what I would have done differently from a development perspective in 2014. In my previous article, 11 Things I wish I knew about Django Before Starting My Company, I started the list. Now it’s time to add to it!

### Use Postgres As Your Primary Data Store

I know in the previous article I was advocating using MongoDB for your primary database, but after 1.5 years of using it, I would no longer suggest it for a number of reasons:

As of Django 1.7, there is still no built in support for `MongoDB` via the Django ORM. You need to utilize Mongoengine/PyMongo. Both of those projects are amazing, but because Django doesn’t support `MongoDB`, you cannot use Django admin — this really sucks and it is one of the reasons why I’ve stopped using `MongoDB` as a primary data store.
As of 12/18/2014, `Postgres` now supports the JSONB data type, which means you can store “documents” in Postgres and run similar queries (with indexing) to MongoDB, without taking a performance hit.
Most third party libraries are useless because they assume you are using the Django ORM. This makes utilizing the Django ecosystem almost impossible. The Django ecosystem is amazing and getting better every day — this is a real bummer.
If you are developing on `OSX`, don’t waste time building Postgres from source, use Postgres App — it will save you a lot of time.

### Build a unit testing infrastructure that makes writing unit tests easy

There are a lot of articles, both positive and negative regarding test driven development(TDD) and `Django`. I’m not taking a side in that fight, but one thing I try to do in every project is setup a framework that makes writing unit tests easy and fun (yes fun). Every project I work on has a class which acts as a mixin for all TestCases. Each unit test (class) then inherits from this mixin and has instance based methods to create data for each test. Fake data is generated using the excellent django_faker project. Data is created in setUp() and removed in tearDown(). If you’re interested in learning more — please read my blog post on unit testing in Django.

### Build RESTful interfaces with Django Tastypie

With the proliferation of client side MVCs such as `Ember.js`, `Backbone.js`, and `Angular.js`, having RESTful support in your web application is now pretty much required. Django has two excellent frameworks which can be used to build REST APIs: django-rest-framework and django-tastypie. Although I get the feeling that django-rest-framework is more popular, I’m still a huge fan of django-tastypie. It seems to be more straight forward, and it has been around a bit longer. With that said, if you are building a public API — absolutely use django-rest-framework, as it provides API documentation for free. If you need help getting started with django-tastypie, check out this tutorial I wrote on how to setup a Django project using it.

### Use Django model field’s `help_text` attribute as a form of documentation

Django model fields accept a help_text attribute which is used in Django forms/admin for display purposes. I highly suggest using it even if you do not plan on utilizing Django forms or Django admin for one reason — It also serves as great form of documentation for your models. If you need to on board a new developer in the future, help_text will save you endless amount of hours bringing someone up to speed.

###  Do not use query parameters in customer facing URLs, use Django’s URL module to force constraints

This may be obvious to experienced web developers, but whenever possible I no longer use HTTP query parameters in urls — I pass them as url parameters instead. My reasoning is you can use Django’s url dispatcher to enforce parameter constraints.

As an example, the following URL:

https://www.example.com/user?id=20

can be re-written as

https://www.example.com/user/20/

You will have to write less type-checking code in your controllers because the Django URL module will throw an HTTP 404 for any non-integers passed in the “id” field. I realize you cannot always do this, but whenever I can do it, I prefer to use this design pattern.

### Use Django’s ORM to enforce database constraints:

I know, I know — another dumb one — but honestly I have seen this time and time again. You can solve a lot of problems for yourself down the road if you use database constraints correctly

if a field can never be null, do not set null=True
use unique_together to enforce unique constraints and preventing dups
use the unique parameter when applicable
set max_length field appropriately
Basically, spend a considerable amount of time designing your database schema correctly, and use Django ORM to enforce this schema to the fullest extent possible! Make it impossible to save incorrect data!

### Use django_model_utils + django_extensions in every project

`django-extensions` is a third party library that provides a number of amazing piece of functionality: printing settings, shell_plus, dumping scripts, encryption, etc. I find myself using more and more of this functionality on a daily basis.

`django-model-utils` contains a bunch of useful functionality not currently available in the Django ORM (or implemented differently): `TimeStampField`, `MonitorField`, `Choices`, etc

Before reinventing the wheel, make sure to throughly check out both of these projects.

### Use Sentry For Both Front-End and Backend Errors Monitoring

`Sentry` is a piece of open source monitoring software written in Django. You can either pay for a subscription at getsentry.com or self host it. Sentry is an absolutely indispensable tool for diagnosing both front-end and back-end errors. You can track all sorts of useful information such has

how many times this error occurred in the past
browser information
time/location of occurrence
stack traces from 500s
404s/403s
front-end javascript undefines
Plans start at $24/month — and this is definitely money well spent.

### Use the django-debug-toolbar for debugging and optimizing your site

`django-debug-toolbar` is an amazing debugging tool. You can use it to track down performance problems in SQL queries, requests, templates, cache, etc. I am not a big advocate of premature optimization, but as soon as things still start to slow down, the `django-debug-toolbar` will help you identify the problems. For more information on how to install this project, please read the following blog post.

### Use Django Custom User Model From Your First Commit

Django 1.5 introduced custom user models and I highly recommend you start with this rather than using Django’s build-in model. Adding fields to the user model is much more intuitive than the alternative method, which was to extend the model using another model with a OneToOne field. If you’ve been using Django for a while, you might find out the that 30 characters allocated for the email and username field on the built-in User model are not enough. If you start with a custom user model, resolving these issues and adding new fields is a migration away! Migrating a built-in user model to a custom user is still possible, but it’s not fun — take my word for it.

### Considering using an alternative to django-admin

Django admin is great — but the design is pretty outdated. If you are building a site for a customer and they are paying for it, there are a number of alternative options that look a lot more professional and are very easy to install. Two of my favorites are `django-grappelli` and `django-suit`. For a larger list, click here:

I tend to blog about Django and Machine Learning from time to time, so if you are interested in this type of content — give me a follow on twitter [@josephmisiti](https://www.twitter.com/josephmisiti)

Shameless Plug: If you need help with Django or Machine learning — I’m available for consulting: [Math & Pencil](http://www.mathandpencil.com)