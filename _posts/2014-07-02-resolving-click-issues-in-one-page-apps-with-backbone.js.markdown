---
layout: post
title:  "Resolving Dead Click Issues In One Page Backbone.js Applications"
date:   2014-07-02
categories: ab
---

#### Introduction

Creating single-page applications with [backbone.js]() and [tastypie]() is really fun. Although I think there are a few downsides to single pages apps (harder to unit tests, harder to debug, more expensive to maintain), the experience you have in a single page app is certainly more enjoyable. It makes scaling a web service a bit easier also because more of the work is done in the client's browser, and not in web-server threads/processes on the cloud.

I recently ran into a weird issue that I have seen more then one time involving clicks in a series of views in a backbone
router. I am going to demostrate how many application worked, and what the solution was. 

The application I created had two panels. The left vertical panel