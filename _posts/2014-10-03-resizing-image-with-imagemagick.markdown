---
layout: post
title:  "Resizing An Image Using Imagemagick"
date:   2014-10-03
categories: imageprocessing
---

[Imagemagick](http://www.imagemagick.org/) is an amazing open-source application that lets you perform all sorts of image & video processing from the command line. On OSX, to install imagemagick, use brew

```
$ brew install imagemagick
```

I often use imagemagick to resize images for blog posts, emails, etc. There are two different ways I use it to resize an image:

#### Resize Images By Percentage

To resize an image by percentage, simply execute the following command

```
$ convert IMAGE.PNG -resize 50%  NEW_IMAGE.PNG
```

#### Resize Images By Dimensions

To resize an image by image dimension, simply execute the following command

```
$ convert IMAGE.PNG -resize 64x64  NEW_IMAGE.PNG
```