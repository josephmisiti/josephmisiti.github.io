---
layout: post
title:  "Getting Started With Ruby on OSX"
date:   2015-02-24
categories: ruby
---

I recently needed to some work in Ruby. I'm a life-long Python dev and I took some notes for anyone else in my position that is starting off and not know what tools to use:

###  Use RVM To Manage Ruby Distributions

RVM is Ruby's version of Python's virtualenv. To install it, you can execute the following commands:

```
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
```

```
\curl -sSL https://get.rvm.io | bash -s stable --ruby
```

After it is installed, execute the following commands:

```
rvm reload
echo rvm_auto_reload_flag=1 >> ~/.rvmrc
```

### Install Specific Version Of Ruby

If you want to see which version of Ruby `rvm` installed by default, simply execute

{% highlight bash %}

(getfetcher)JOSEPH-MISITI:contract josephmisiti$ rvm list
ruby-2.1.5 is not installed.
To install do: 'rvm install ruby-2.1.5'
RVM reloaded!

rvm rubies

   ruby-2.1.1 [ x86_64 ]
=* ruby-2.2.0 [ x86_64 ]

# => - current
# =* - current && default
#  * - default
{% endhighlight %}


If you are working on an existing Rails app, the `GemSpec` file will specify a specifc version of Ruby:

```
Your Ruby version is 2.2.0, but your Gemfile specified 2.1.5
```

This is no problem, just install that specific version of ruby using `rvm` as follows:

{% highlight bash %}
(getfetcher)JOSEPH-MISITI:contract josephmisiti$ rvm install 2.1.5
Searching for binary rubies, this might take some time.
Found remote file https://rvm_io.global.ssl.fastly.net/binaries/osx/10.9/x86_64/ruby-2.1.5.tar.bz2
Checking requirements for osx.
Certificates in '/usr/local/etc/openssl/cert.pem' are already up to date.
Requirements installation successful.
ruby-2.1.5 - #configure
ruby-2.1.5 - #download
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
{% endhighlight %}


### Install Third Party Libraries

If you are used to Python, you are used to executing

```
pip install -r requirements.txt
```

The analogous command in Ruby is:

```
bundle install
```

### Running Unit Tests

If you want to run all the unit tests in a Rails project, then you can easily do that with the following command

```
bundle exec foreman run rspec
```

### Switching Between Multiple Versions of Ruby

If you need to switch between multiple Ruby installations using `rvm`, you can do it with the following command

```
rvm use 2.1.1
```






