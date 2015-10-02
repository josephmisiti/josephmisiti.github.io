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

If you can successfully connect, your all set, you need to setup your tables. I used the `psycopg2` to setup these tables, but you could of course manually enter them into the REPL. If you want to take my approach first, assuming you are running on ubuntu, install the `postgres` requirements

```
sudo apt-get install -y postgresql-client-9.3 libpq-dev
```

Now install `psycopg2` using pip:

```
pip install psycopg2
```


