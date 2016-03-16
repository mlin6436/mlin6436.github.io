---
layout: post
title: "Upgrade SVN on Centos"
tags: ["svn", "subversion", "centos", "sqlite", "apr", "apr-util", "serf", "https", "certificate"]
---

<div class="message">
After searching through the web, I managed to find bits and pieces about upgrading subversion from 1.6 to 1.7 on CentOS. Unfortunately, it is like unraveling a puzzle, every blog starts from a slightly different angle and came across different blocks. I was strapped in perpetual trying and failing because of this. So I thought I better summarise and share my whole experience, along with the pitfalls while trying to work it out. The upgrade, in a nutshell, is a process of uninstalling the current version of svn, and download, compile and install the target version.
</div>

# Remove current svn

That's right! You heard me, be brave and remove svn using:

{% highlight bash %}
$ yum remove subversion
{% endhighlight %}

# Download target version of svn

Go to this location, download and unzip the package there:

{% highlight bash %}
$ cd /usr/local/src/
$ mkdir subversion
$ wget http://apache.mirrors.timporter.net/subversion/subversion-1.7.16.tar.gz
$ tar zxf subversion-1.7.16.tar.gz -C subversion
{% endhighlight %}

Surely you might come across problem like, you can't download the specific version because it does not exist. Fear not, just go to this [mirror](http://apache.mirrors.timporter.net/subversion/), and find the version you want and wget it again.

# Download other packages for svn

In the 'subversion' folder you've just created, we need to add two other packages 'apr' and 'apr-util' before svn can be compiled.

{% highlight bash %}
$ cd /usr/local/src/subversion
$ mkdir apr
$ wget http://apache.mirrors.tds.net/apr/apr-1.5.0.tar.gz
$ tar zxf apr-1.5.0.tar.gz -C apr
$ mkdir apr-util
$ wget http://apache.mirrors.tds.net/apr/apr-util-1.5.3.tar.gz  
$ tar zxf apr-util-1.5.3.tar.gz
{% endhighlight %}

Again, you should be able to find a more up-to-date version [here](http://apache.mirrors.tds.net/apr/) if necessary.

# Configure subversion

Run the script for auto-configuration:

{% highlight bash %}
$ ./configure
{% endhighlight %}

## 1. Oops, you might need sqlite

The steps have been straight forward so far, yet configure may fail because of the following reason:

{% highlight yaml %}
configure: checking sqlite library
checking sqlite amalgamation... no
checking sqlite3.h usability... no
checking sqlite3.h presence... no
checking for sqlite3.h... no
checking sqlite library version (via pkg-config)... no

An appropriate version of sqlite could not be found.  We recommmend
3.7.6.3, but require at least 3.6.18.
Please either install a newer sqlite on this system

or

get the sqlite 3.7.6.3 amalgamation from:
    http://www.sqlite.org/sqlite-amalgamation-3.7.6.3.tar.gz
unpack the archive using tar/gunzip and copy sqlite3.c from the
resulting directory to:
/usr/local/src/subversion-1.7.14/sqlite-amalgamation/sqlite3.c

configure: error: Subversion requires SQLite
{% endhighlight %}

Now have a look at your sqlite version:

{% highlight bash %}
$ sqlite3 -version
{% endhighlight %}

Mine comes up as '3.3.6', which is well out of date apparently. If you are sure you've got the correct version, try to run svn configure again, if still no joy, carry on reading this section.

Now you need to go download and upgrade your sqlite:

{% highlight bash %}
$ wget -q -O - http://www.atomicorp.com/installers/atomic | sh
$ yum --enablerepo=atomic upgrade sqlite
{% endhighlight %}

Even though this step does work quite well for me, keep on trying to find the correct package if the link let you down.

Once you can confirm you have sqlite 3.7.0.1 or later, it is all good to go... to the next step.

## 2. Yet another Oops, you might need to install sqlite manually

Sometimes, even if everything seems to be correct, it just won't work. That leaves you the last option: get the sqlite files and put it in the directory specified just for svn configuration's sake:

{% highlight bash %}
$ cd /usr/local/src/subversion
$ mkdir sqlite-amalgamation
$ wget http://www.sqlite.org/2013/sqlite-amalgamation-3080200.zip
$ tar zxf sqlite-amalgamation-3080200.zip -C sqlite-amalgamation
{% endhighlight %}

# Sit back, have a cup of tea and let subversion do the rest

At last, you should have ingredient subversion needs, and should be able to compile and install it. Otherwise, I seriously have no idea what you are up against, so go cry for help [elsewhere](http://stackoverflow.com/)!

{% highlight bash %}
$ make && make install
{% endhighlight %}

Check the subversion vesion to make sure it is properly installed, and you might want to reboot the box as well.

# Upgrade the existing project

By running the following command, you can then bring your old svn projects back to life.

{% highlight bash %}
$ svn upgrade
{% endhighlight %}

# Configure certification for svn

For those who live under HTTPS protocol, you will need one extra thing so that you can live happily ever after, Serf. Serf is the package required by subversion to be able to use HTTPS communication with your P12 certificate.

By following the previous installation steps and reconfigure subversion, serf can then be put into use.

{% highlight bash %}
$ cd /usr/local/src/subversion
$ mkdir serf
$ wget http://serf.googlecode.com/files/serf-1.2.1.zip
$ tar zxf serf-1.2.1.zip
$ ./configure --with-serf=/usr/local/src/subversion/serf
$ make && make install
{% endhighlight %}

After which, you need to declare your certificate as well.

{% highlight bash %}
$ echo 'ssl-client-cert-file = <P12>' >> ~/.subversion/servers
$ echo 'ssl-client-cert-password = <***>' >> ~/.subversion/servers
{% endhighlight %}

# References

[Centos Upgrade Subversion to Version 1.7](http://nixkb.org/2012/07/centos-upgrade-subversion-to-version-1-7/)

[Upgrade Subversion 1.7 on Centos](http://superlinuxadmin.wordpress.com/2012/02/11/upgrade-subversion-1-7-on-centos/)

[Install Subversion SVN from Source on Centos Redhat 64 bit](http://rahulsoni.me/2011/11/install-subversion-svn-from-source-on-centos-redhat-64-bit/)
