---
layout: post
title:  "Restore A Deleted File With Git"
date:   2014-5-01
---

Have you ever deleted a file and a few commits later realized you actually needed it in your project because of a hidden depedencies you did not see? Here is a simple bash command that will restore any deleted file by filename.

{% highlight bash %}
function grf(){
    # restores file that was deleted. to run
    # cd into the directory of the file and type
    # grf filename-that-was-deleted
    git checkout $(git rev-list -n 1 HEAD -- "$1")^ -- "$1"
}
{% endhighlight %}

The command uses git to find the revision it was deleted and then restores it. You will need to commit the file once it is restored.