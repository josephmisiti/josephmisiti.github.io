---
layout: post
title:  "Getting Started With Amazon Redshift"
date:   2015-09-28
categories: etl,redshift,amazon,devops
---

I recently worked on a project in which I was asked to migrate someones analytics database into a Redshift cluster. The backend of this website was using Django, so all of my development was done in Python.

A had a couple of goals before starting the project:

1. Keep everything in Python
1. Untilize Django's ORM when possible
1. Make migrations easy