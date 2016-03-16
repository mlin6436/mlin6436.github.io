---
layout: post
title: "Test Environment Setup in Visual Studio"
tags: ["test", "nunit", "setup", "vs", "visual studio", "2012", "2013"]
---

<div class="message">
Unit testing is good, <a href="http://martinfowler.com/bliki/TestDrivenDevelopment.html">TDD</a> is even better. To smooth out the workflow in Visual Studio, I will present some of my favourite ways of setting up NUnit in my projects, and hopefully that helps win some skeptics whom think it is too much hassle doing TDD back.
</div>

# Project setup

In a typical project setup, you should have one test project per source project with [NUnit](https://www.nuget.org/packages/nunit/) referenced.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-01.png" width="100%" description="A typical source and test setup" %}

# NUnit Executable

One way of using NUnit is quite manual, which you need to install [NUnit](http://nunit.org/?p=download) to your system.

By configuring a test runner with `.nunit` extension in the following format,

{% highlight xml %}
<NUnitProject>
  <Settings activeconfig="Debug" />
  <Config name="Debug" binpathtype="Auto">
    <assembly path="Playground.Tests\bin\Debug\Playground.Tests.dll" />
  </Config>
  <Config name="Release" binpathtype="Auto">
    <assembly path="Playground.Tests\bin\Release\Playground.Tests.dll" />
  </Config>
</NUnitProject>
{% endhighlight %}

You now have an executable NUnit file. When opening up the file, you should be one click away from running the tests.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-05.png" width="100%" description="NUnit control pannel" %}

Click `Run`, and ta-da.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-06.png" width="100%" description="NUnit control pannel after running the tests" %}

# NUnit Extension in Visual Studio

A more seamless way of using NUnit in Visual Studio is: go to `TOOLS` -> `Extensions and Updates`, find the `NUnit Test Adapter` in Visual Studio online gallery and install it.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-02.png" width="100%" description="NUnit test adapter plugin" %}

Now again in Visual Studio, if you navigate to `TEST` -> `Windows` -> `Test Explorer`, you should be able to see `Test Explorer` panel hanging over Visual Studio.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-03.png" width="100%" description="Test Explorer window with a dummy test I created" %}

With the `Run All` and other execution options available, you should try to push the buttons as often as possible when developing. On a good day or in your dream, you will always get this.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-04.png" width="100%" description="Test Explorer window with a dummy test I created" %}

# Resharper

The last and the most expensive way, is to install [Resharper](http://www.jetbrains.com/resharper/download/). It's undeniably costly, yet you are more likely to gain a lot of time in return in the long run.

And since we are in Resharper world, you can either use Reshaper shortcut `Ctrl+ T, R`, or more generic Visual Studio shortcut `Alt+ R, U, N` to execute the tests like a pro.

And you surely will not miss what pops up onto your screen.

{% include image.html url="/media/2014-09-04-test-environment-setup-in-vs/test-07.png" width="100%" description="Resharper Unit Test Session" %}

# Conclusion

In my opinion, even though Resharper is not a cheap option, but you do get a lot of other cool refactor functionalities, and smoother workflow.

If budget is a concern, to setup your own NUnit runners is still going to make your life a lot easier, and I will personally go for the NUnit extension to make testing independent from the PC being used, and save the hassle of re-configuration whenever you need to add a new project.
