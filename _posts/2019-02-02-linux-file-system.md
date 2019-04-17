---
layout: post
title: "Linux File System"
tags: ["linux", "unix", "file system", "command line"]
---

<div class="message">
Working with Linux/Unix system is a pleasant experience, even though it requires a slightly steeper learning curve in the beginning. in comparison to Windows, it is totally worthwhile. For those who just started working with Linux/Unix system or wants to know more, I found it useful to understand a few basic concepts, at least I knew that when I started.
</div>

# Directory is a file

Hardly surprising, every OS would have the concept of `directory` or `folder` of some short, which is a recursive structure existed for the purpose of storing more directories or files. This has some interesting implications: each directory has two special files named `.` and `..`, where the single dot refers to the current directory itself, the double-dot means the parent of the current directory.

So, what's the maximum allowed depth of sub directories in different OS? I will leave that to you to find out.

# A summary of system directories

In a typical Linux/Unix system, you can normally see the following directories:

{% include image.html url="/media/2019-02-02-linux-file-system/linux-file-system.png" width="100%" description="Linux file system" %}

I will list out a few commonly used directories for your reference:

- `/` => the root of all file system
- `/bin` => binary, it is where the essential tools live
- `/dev` => device, because everything in Linux system is represented by a file, including hardware, any newly mounted hardware devices will be listed here
- `/etc` => configuration files for the system
- `/home` => all system users' files
- `/lib` => library, essential system libraries used by programs in `/bin`
- `/proc` => process, files that contain information of current system processes
- `/root` => root user's home directory, the name of the root user is "root", and has access to everything on the system
- `/sbin` => system binaries, essential system administrator tools
- `/tmp` => temporary files, typically get cleared after reboot
- `/usr` => universal system resources, files such icons, programs used by desktop or users
- `/boot` => boot files, file used by kernel to boot the machine
- `/opt` => optional, some optional program live
- `/var` => variable data files, logs, temporary files for the system, caches, backups are stored here

Every directory listed here, except "home", requires administrator privilege to access. For normal system user, it has access and normally only operates under its own directory under "home".

# Hidden files

Files, begin with "." in the name, are hidden files. Most of the hidden files contain configurations, such as ".bash_history", it contains a list of command used.

# Useful commands

The true potential of Linux/Unix system being a productivity boost can be unleashed with an effecient use of commands.

Navigation:

{% highlight bash %}
cd / // go to root folder
cd .. // go to the parent folder
pwd // find out absolute path of the current directory
{% endhighlight %}

List directory content:

{% highlight bash %}
ls // list the content of current directory
ls -lsa // list all files, including hidden files (-a), with file attributes (-l) and size of each file (-s)
{% endhighlight %}

Make a file:

{% highlight bash %}
touch file // make an empty file named "file"
{% endhighlight %}

Make a directory:

{% highlight bash %}
mkdir dir // make a directory called "dir"
{% endhighlight %}

Make a directory tree:

{% highlight bash %}
mkdir -p temp/sub1 // make the directory "temp" with a sub-directory "sub1"
{% endhighlight %}

Copy files and directories:

{% highlight bash %}
cp current target // copy "current" file to a file called "target"
cp -R current_dir target_dir // copy "current_dir" directory and its files and subdirectories to a directory called "target_dir"
{% endhighlight %}

Move/rename files and directories:

{% highlight bash %}
mv current target // rename "current" file to "target"
mv current ../target // move "current" file to "target" file in the parent directory
mv current_dir target_dir // rename "current_dir" directory to "target_dir"
{% endhighlight %}

Remove files and directories:

{% highlight bash %}
rm file // remove "file"
rm -r dir // remove "directory" and all its content
{% endhighlight %}

__Important__: because `rm` removes the file permanently, think twice before removing it. Alternatively, use a softer approach with option `-i`, or create an alias for `rm` to it moves files to a trash bin, and clean it afterwards.

By now, you should be good to go explore the fascinating Linxu/Unix system for yourselves.