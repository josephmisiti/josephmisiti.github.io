---
layout: post
title:  "Settings Up Front-End Unit Tests With Jasmine And Grunt.js"
date:   2014-10-24
categories: javascript,front-end,unittests,grunt.sj
---

As the web moves from backend applications to front-end applications, more front-end unit tests are going to be written. I write most of my front-end applications using backbone.js, and only recently decided to start actually unit testing this code. For one thing, its pretty easy to tell if front-end code is working, as your website isnt broken. Some of the most popular websites on the internet have even admitted at some point that they didnt write unit tests: Pinterest claimed they unit tested their UI by ["clicking around"](https://news.ycombinator.com/item?id=3541317); I have read in other places that for a number of years Facebook did not have any front end unit tests. Regardless, once you get a decent infrastructure up-and-running, writing front end unit tests can be fun, and as long as you do not need to open a new browser window to run them, they can be part of your continuous integration cycle.

Since I am running a number of large-scale backbone applications, I decide to start writing my unit tests using [Jasmine](http://jasmine.github.io/). Before I deploy my application with a new update, I run all of these unit tests with a headless browser (Phantom.js) using the node-based task manager Grunt.js.