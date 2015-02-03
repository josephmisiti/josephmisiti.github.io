---
layout: post
title:  "Video Processing With FFMPEG"
date:   2015-02-03
categories: videoprocessing
---

I recently was messing around with some computer vision algorithms and wanted to run them across some test data.
The first place I went looking it was youtube.com. I installed the excellent [youtube-dl](http://rg3.github.io/youtube-dl/) library that will let 
me download any video from the command line by simply providing a url

    youtube-ld https://www.youtube.com/watch?v=dEe9Q2KQvM4
	
Once the video is downloaded, you need to convert if from video to a stream of images: This can be done very easily with FFMPEG like this:

```
ffmpeg -i <VIDEO> -r <FPS> <OUTPUT_FOLDER>/prefix_%05d.png
```

And if you want to rotate the images at the same time, added the transpose flag

```
ffmpeg -i <VIDEO> -r <FPS> -vf "transpose=1" <OUTPUT_FOLDER>/prefix_%05d.png
```

Once you have processed these images your algorithm, you need to resave them into a new directory with the same
naming scheme and you can re-combine them into a video using the following command

```
cat <IMAGE_DIR>/*.jpg | ffmpeg -f image2pipe -r 1 -vcodec mjpeg -i - -vcodec libx264 OUTPUT.mp4
```

Now take a look at OUTPUT.mp4 to see your processed video.