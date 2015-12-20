---
layout: post
title: "Manage Java Version on Unix/Linux"
tags: ["java", "manage", "switch", "version", "maven", "unix", "linux", "jenv", "openjdk", "brew", "brew cask"]
---

<div class="message">
As many would have experienced, Java version is one of the most common problem when developing software, and probably the most painful type. Especially if you have legacy projects written in Java 7, while you want to take advantages of what Java 8 has to offer, switching between versions is an absolute hell. This time, I wish to offer some insights into solving the problem on Unix/Linux from my perspective, as well as some practical solutions to lift this concern.
</div>

# A Ready Solution for Unix

Lucky you if you have to use both Java 7 and 8 for different projects on the same dev box. I can totally relate the pain.

Initially, I was hoping [brew](http://brew.sh/) would be able to magically sort out this problem for me. And boy, how naive was I. After few digging, it is not hard to find out brew is surely not the tool for this kind of job.

This is how [jenv](http://www.jenv.be/) comes to the rescue. Obviously, `jenv` is a Java version managing tool. How `jenv` works is by setting up a shim in your home directory, and by adding the following lines to your `.bash_profile`, it then takes over and finds the right Java version to use globally or in a specific directory.

{% highlight bash %}
$ echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(jenv init -)"' >> ~/.bash_profile
{% endhighlight %}

Of course, you need to have different Java versions installed, and this is something [brew cask](http://caskroom.io/) will happily handle.

{% highlight bash %}
$ brew tap caskroom/versions
$ brew cask install java7
$ brew cask install java # this is default to Java 8
{% endhighlight %}

The next important thing is to make sure `jenv` is aware of the JDKs installed by adding them to `jenv`. The Java versions are normally installed under `/Library/Java/JavaVirtualMachines/`.

{% highlight bash %}
$ jenv add /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home
{% endhighlight %}

Using `jenv` is in fact really easy and pleasant with command line.

{% highlight bash %}
$ jenv versions # this should list all the available Java versions
$ jenv global java8 # this should set Java 8 for the whole dev environment
$ jenv locally java7 # this should configure Java 7 for the directory
{% endhighlight %}

This seems like a happy ever after love story, but think again, is it though?

> The likelihood is the project is now configured to use right Java version, but other tools, such as Maven, may disagree. The reason being as `jenv` shim takes over `.bash_profile`, it does not physically reconfigure Java version installed in `/usr/bin`. This will cause a hell lot of confusion while the project is configured to use one version of Java, yet Maven uses another version of Java by default.

There are different approaches to solving this problem, but rather than living with a tool I have no extended understanding of (which has in fact screwed me over more than once), I came up with a home grown solution.

# A Home Grown Solution for Unix

Like I mentioned before, the downside of `jenv` is that the default version of Java remains unchanged, which causes lots of confusion.

> To tackle this problem, my plan is to change default Java environment. The default Java executables configured in `/usr/bin` are nothing but [symlinks](https://en.wikipedia.org/wiki/Symbolic_link), which means despite the fact that they should remain the same (if you want anything to work at all) on the surface, by pointing the executables to the version I want can archive the Java version switching just the same.

To make this work, first of all, you need to install different Java versions needed through our best friend `brew cask`. Then, replace all the current Java binaries in `/usr/bin` with the target version.

{% highlight bash %}
$ sudo unlink /usr/bin/java
$ sudo unlink /usr/bin/javac
$ sudo unlink /usr/bin/javadoc
$ sudo unlink /usr/bin/javah
$ sudo unlink /usr/bin/javap
$ sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/java /usr/bin/java
$ sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/javac /usr/bin/javac
$ sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/javadoc /usr/bin/javadoc
$ sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/javah /usr/bin/javah
$ sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.8.0_51.jdk/Contents/Home/bin/javap /usr/bin/javap
{% endhighlight %}

Do remember to check your Java version to confirm the great success.

{% highlight bash %}
$ java -version
{% endhighlight %}

For whoever is not confident of what they are doing, I'd encourage you to take a copy of existing configs before trashing them.

As for the lazy ones, you know I must have a [script](https://github.com/mlin6436/cornerstone/blob/master/macos/javaversion) to do this kind of dirty work. Yes, you are right. But also bear in mind, I have only come so far to make the script work for switching between Java 7 and 8 on a Mac with the listed Java versions installed.

After going through my solution, I am sure everyone would be able to create a version tailored to their systems.

> One last tip I would give is: make sure Java version to use is compatible with the tools. I have colleagues trying to configure Maven 3.2+ to use Java 1.6, and I just can't stress enough how big a disaster that is.

# A Piece of Cake in Linux

Comparing to Unix, switching Java versions (OpenJDK) on Ubuntu 14.04 is like walking in the park.

{% highlight bash %}
$ sudo apt-get update
$ sudo apt-get install openjdk-8-jdk
$ sudo update-alternatives --config java
$ sudo update-alternatives --config javac
{% endhighlight %}

It is quite unlikely, but if for any odds the package is not available, try running `sudo add-apt-repository ppa:openjdk-r/ppa` first.

After all, the solution is solid and widely used by the community, so really, don't try to be creative unless you are really bored.

Happy hacking!
