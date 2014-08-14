---
layout: post
title: "DiskPart Command in Windows"
tags: ["diskpart", "command", "windows", "sysadmin", "disk management"]
---

<div class="message">
As an automation process for disk configuration and manipulation, using the command line DiskPart is a must as a SysAdmin.
</div>

#To get you started

[DiskPart](http://technet.microsoft.com/en-gb/library/cc766465.aspx) allows us to manage disks, partitions or volumes just like [Disk Management](http://www.disk-partition.com/resource/disk-management-windows7.html) tool does. To get started, type in `cmd` after `start + r` to open the command prompt, and type in `diskpart`.

{% include image.html url="/media/2013-05-29-diskpart-command-in-windows/diskpart-01.png" width="100%" description="Diskpart command" %}

In order to check the status of volumes, you can use the `rescan` to kick start the process and it will bring you any changes to the disk.

{% include image.html url="/media/2013-05-29-diskpart-command-in-windows/diskpart-02.png" width="100%" description="Rescan command" %}

To list all volumes on this device, use `list volume`.

{% include image.html url="/media/2013-05-29-diskpart-command-in-windows/diskpart-03.png" width="100%" description="List volume command" %}

Similarly, if we interested in disk or partition information, `list disk` and `list partition` are always available.

#Roll like a sysadmin

To begin doing some damage, you need to first locate the target using commands like `select disk 1`. The details are as followed:

{% highlight bash %}
SELECT [DISK|VOLUME|PARTITION] <ID>
{% endhighlight %}

Once you made a selection, it will not change until you specify otherwise. So it is always worth making sure to what you are performing the actions. Better safe than sorry, right?

Also, there is a hierarchy you should be aware of: once a volume is selected, the related disk and partition would also have the focus. Once a partition is selected, the related volume would also have the focus.

Given the right command, which you can always find via `help <action-name>`(if you know the specific action that's available) or `help` (to get a full list of actions), you are free to do whatever you need to do.

Just to make life a little easier, you can also script the operations and save them into a `.bat` file, then you can run the commands with a figure of click.
