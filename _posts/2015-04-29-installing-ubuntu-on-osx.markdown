---
layout: post
title:  "Installing Ubuntu 14.04 LTS on a Macbook Air"
date:   2015-04-30
---

I recently went through the horrible experience of installing UBUNTU on my macbook air. Now, full disclaimer, the shittiness of this experience was completely my own making - I refused to dual boot my computer - I wanted to turn on my macbook air in boot directly into UBUNTU. 

For anyone trying to do this, my only advice is **make sure you install [refit](http://refit.sourceforge.net/) before you reformat your hard drive**. If you install reFit first, then you will always boot into a boot menu, which will let you toggle between UBUNTU and OSX. Even if OSX is not install, you can still toggle to UBUNTU and boot into it very easily. If you're an idiot like me an erased the entire hard drive, you lose the ability to install the boot menu and have to manually go into the `grub` loader and edit memory addresses to get this to work properly. 

So to sum it up, if you want single-boot UBUNTU on a macbook, install reFit first. If you are reading this and it is too late (and you dont want to reinstall OSX and then refit,etc), then following [these directions](https://medium.com/@PhilPlckthun/ubuntu-14-10-running-on-my-macbook-18991a697ae0) word-for-word and you will be all set. On a side note, UBUNTU actually seems to run better than OSX on my macbook air so I am pleased with the decision.