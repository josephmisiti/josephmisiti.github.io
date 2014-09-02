---
layout: post
title:  "Changing Default Location of OSX ScreenShotss"
date:   2014-4-20
---


Are you sick of the results of your `CTRL+SHFT+4` (screenshots) on your desktop? Here is a fix to dump them into the `~/Downloads` folder:

```
$ defaults write com.apple.screencapture location ~/Downloads/
```
```
$ killall SystemUIServer
```