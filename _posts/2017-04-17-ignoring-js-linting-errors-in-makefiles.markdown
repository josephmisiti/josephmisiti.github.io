---
layout: post
title:  'Ignoring js linting errors in makefiles'
date:   2017-04-17
categories: jekyll update
---
I use make as a part of my deploy process. I create make commands like `make deploy-dev` to deploy to development
and `make deploy-staging` to deploy to staging. You get that picture. One of the things I needed to figure out was how to tell `make` to ignore linting errors when I am compressing my assets using `webpack`

Here are some example commands taken directly from my `Makefile`

```
run-yarn:
	yarn

deploy-dev: run-yarn
	rm static/build/*
	NODE_ENV='development' webpack -p --progress --colors 2>/dev/null; true
	python deploy-site.py dev

deploy-staging: run-yarn
	rm static/build/*
	NODE_ENV='staging' webpack -p --progress --colors 2>/dev/null; true
	python deploy-site.py staging
```

If you want to ignore errors, pipe the stdout (or stderr) to null and return true using `>/dev/null; true`
