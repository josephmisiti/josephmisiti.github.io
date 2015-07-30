---
layout: post
title:  "Settings Up VSFTP on UBUNTU"
date:   2015-07-30
categories: ftp,devops
---

Here are some instructions for settings up [VSFTP](https://en.wikipedia.org/wiki/Vsftpd) on UBUNTU (14.04 LTS).
Assuming you are `root`, login and update `apt-get`:

```
apt-get update
apt-get upgrade
```

Now install [VSFTP](https://en.wikipedia.org/wiki/Vsftpd)

```
apt-get -y install vsftpd
```

Now you need to make a few edits to `/etc/vsftpd.conf`, uncomment, change, or add the following parameters:

{% highlight bash %}
anonymous_enable=NO
local_enable=YES
write_enable=YES
chroot_local_user=YES
{% endhighlight %}

Save and exit that file. Now, you shoudl create a new user which will only have access to FTP. I am creating a new user here because my current user is using a private key for login and I have SSH login disabled.

```
adduser <USERNAME>
```

Following the prompts UBUNTU gives you.

Finally, lets create a directory for to upload your files to:

{% highlight bash %}
mkdir /home/<USERNAME>/files
chown <USERNAME>:<USERNAME> /home/<USERNAME>
{% endhighlight %}


Restart the deamon:

```
sudo service vsftpd restart
```

Now use your favorite FTP client ([FileZilla](https://filezilla-project.org/)) or commandline to login and trasfer files.


