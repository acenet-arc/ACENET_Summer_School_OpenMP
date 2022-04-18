---
layout: page
title: Setup
permalink: /setup/
---

While you could use any appropriately configured Linux system to follow along with
this course, it has been developed and tested on Compute Canada general-purpose
clusters and so will work best if you have a Compute Canada account. We have
resources reserved for the 2019 workshop on Béluga, `beluga.computecanada.ca`.

If you are running either Linux or Mac OSX, you can log in by opening a terminal
window and logging in with `ssh username@cluster.computecanada.ca`. If you are
running Windows, then you should use PuTTY or MobaXTerm.

Once you are logged in, you can request an interactive session on a compute node with the command

~~~
salloc --account=acenet-wa --reservation=acenet-wr_cpu --time=8:00:00 --cpus-per-task=4 
~~~
{: .language-bash }

Please remember to end your session when you are finished, with `exit` or control-D.
