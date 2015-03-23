---
layout: post
title:  "Removing a random set of git commits from a remote branch"
date:   2015-02-09
categories: git
---

### The Situation

A developer (developer A) on your team commited to a branch you are working off of (`staging` in this case), and you did not want the commits on `staging` yet, as they were not ready for release. You accidently pulled, merged, and pushed (because you are an idiot and didnt catch it) and now you have polluted the global, remote `staging` branch.

How do you remove the series of commits?

### The Solution (non-conventional)

First, assuming you are sitting on the polluted `staging` branch locally (run `git branch`), create a new branch called `old_staging`, branching off of `staging`:

```
	git checkout -b old_staging
```

Now all the commits your about to blow away are save locally at least. Next, find the last commmit (use `git log` on `staging` that was not polluted (basically the last commit before developer A's commits). Use that git hash to reset HEAD

```
	git reset --hard <LAST GOOD HASH>
```

Now, blow away his changes, but make sure __he knows you're doing it__ so he can save them locally also.


```
	git push -f
```

Now your remote is no longer polluted, but any changes you made since <LAST GOOD HASH> are gone, but that's easy to fix now also, just use `git cherry-pick` to pull them over into your branch	

{% highlight bash %}

git cherry-pick <NEW-HASH1>
git cherry-pick <NEW-HASH2>
git cherry-pick <NEW-HASH3>
....

{% endhighlight %}


Finally, push your changes up to remote

```
	git push
```

Boom - you've solved your problem, in a somewhat non-conventional way.

Disclaimer: you might only want to do this if:

1. Your team is very small
1. You own the company