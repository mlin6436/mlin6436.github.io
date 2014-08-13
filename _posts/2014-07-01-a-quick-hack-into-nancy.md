---
layout: post
title: "A Quick Hack Into Nancy"
tags: ["nancy", ".net", "http", "mono"]
---

<div class="message">
Nancy, as the next generation framework for building lightweight HTTP based services in .NET, after ASP.NET web form, MVC and WebAPI, I'd like to show you how to kick start it and some useful bits and pieces.
</div>

For whoever is still wondering who Nancy is or what Nancy does, [here](https://github.com/NancyFx/Nancy) is a good place to start.

To jump staight into the project as usual, Visual Studio is the place we go. Since I am using Visual Studio 2013, there are some slight changes to the way web application frameworks are created.

{% include image.html url="/media/2014-07-01-a-quick-hack-into-nancy/hacknancy-1.png" width="100%" description="Create a web project in Visual Studio 2013" %}

The web forms, MVC and WebAPI frameworks are now in a sub category in Web Application. And I really don't know what is Microsoft's plan for this.

But we just need an empty web project to kick off.

{% include image.html url="/media/2014-07-01-a-quick-hack-into-nancy/hacknancy-2.png" width="100%" description="The templates in web project" %}

A clean slate is all we have and need, minimum references and a web.config.

{% include image.html url="/media/2014-07-01-a-quick-hack-into-nancy/hacknancy-3.png" width="100%" description="The empty web project stucture" %}

Next up, you need to pull in some packages, including 'Nancy' and the hosting strategy. Since IIS would still be the quickest way to proceed with this, I would be using it. But that should not stop you from choosing the right or coolest host, like [OWIN](http://owin.org/).

{% highlight bash %}
Install-Package Nancy
Install-Package Nancy.Hosting.Aspnet
{% endhighlight %}

And then you will find some smurfs have already help you populate the web.config.

{% include image.html url="/media/2014-07-01-a-quick-hack-into-nancy/hacknancy-4.png" width="100%" description="Web.config after Nancy is installed" %}

Bear in mind, you will get into trouble if you don't have the hosting handlers declared in the config.

Till now, you are all good to go. And what a useless blog, right? Hold your horses, where's always something more...

There is a trap that can easily lure some self-declared smartass in. If you find yourself got shown the following error message, it means that you just created your project using 'Nancy', and that is not acceptable!

{% include image.html url="/media/2014-07-01-a-quick-hack-into-nancy/hacknancy-5.png" width="100%" description="The bizarre error message" %}

Because namespace clashes with Nancy package, JIT will confuse the object type you try to load is originated from the project you created, and of course it will not find anything!

Since some people just have to learn it the hard way, and googling is not much help in this case, I hope this helps someone out of his misery at least.
