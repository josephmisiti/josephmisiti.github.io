---
layout: post
title:  "Resizing EBS Volumes on EC2 using UBUNTU 11.04"
date:   2013-01-03
---

If you need to re-size (UBUNTU 11.04) an ebs volume mounted at lets say `/db`, you can do so using the following steps:

1. Shutdown all services accessing `/db`

1. Unmount the volume (`umount /db`)

1. Take a snapshot of the volume using the EC2 management console

1. Create a new volume from this snapshot with more space

1. Attach this volume to your running instance

1. Use `fdisk -l` to find where the device is mounted (/dev/xvdf)

1. Run `e2fsck -f /dev/xvdf`

1. Run `sudo resize2fs /dev/xvdf`

1. Remount and you're good to go: `mount /db`