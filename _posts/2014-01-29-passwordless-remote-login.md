---
layout: post
title: "Passwordless Remote Login"
tags: ["linux", "ssh", "login", "password", "passwordless"]
---

<div class="message">
The intention is simple: just let me in without asking for password again and again!
</div>

#Generate a RSA key

{% highlight bash %}
$ ssh-keygen -t rsa -C <email>
{% endhighlight %}

Accept the default location and DO NOT give a password to it.

#Copy the RSA key to the remote machine

{% highlight bash %}
$ cat ~/.ssh/id_rsa.pub | ssh <user>@<ip> 'cat >> ~/.ssh/authorized_keys'
{% endhighlight %}

So far, it is all done. You will not be asked to type in any password next time you login.

#A step further

It would be great if can we save the time on typing the hideous 'SSH' and 'user@ip' as well, wouldn't it? It can be easily done by setting up an alias.

{% highlight bash %}
$ alias apple='ssh <user>@<ip>' >> ~/.bashrc
{% endhighlight %}

or `.bash_profile` in Mac. And there you go, `apple` is your new login!
