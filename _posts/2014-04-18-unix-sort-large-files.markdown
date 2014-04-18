---
layout: post
title:  "Using The UNIX Command Line To Sort Large Files" 
date:   2014-4-18
---

If you are are you using UNIX to sort a large file, you might run into the following error

```
sort: write failed: /tmp/sortbizrs3: No space left on device
```
This is because sort uses the /tmp directory by default. If you are on EC2, attach an EBS drive, mount it,
and the you can complete the sort with:

```
sort -T /path/to/ebs/ LARGE_FILE
```	
