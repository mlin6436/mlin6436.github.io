---
layout: post
title: "When Should You Refactor?"
tags: ["refactor", "tdd", "reusability", "testability", "design pattern", "quality code", "solid", "single responsibility", "framework"]
---

<div class="message">
Refactoring code is a tricky business. Doing it too early, you are simply creating work which may or may not be needed. Doing it too late, you end up with a bunch of spaghetti code, which is a right pain in the neck to work with. I am going to provide some observations from the projects I've worked on over the years, and hopefully they would be of help.
</div>

{% include image.html url="/media/2015-10-04-when-should-you-refactor/good_code.png" width="50%" description="Good Code?" %}

Take an application which provides RESTful endpoints for example, it is so easy to just stick everything inside that poor controller and see it implodes.

{% highlight scala %}

import play.api.libs.ws._

class MyController extends Controller {
  def ping: Future[Response] = {
    val databaseManager = new DatabaseManager
    val urls: String = databaseManager.urls
    val response: Future[WSResponse] = urls map { url =>
      WS.url(url)
        .withHeaders("Accept" -> "application/json")
        .withRequestTimeout(timeout)
        .get()
    }
    val responseParser = new ResponseParser
    val status: Future[Response] = response map { res =>
      responseParser.parse(res)
    }
    val overallStatus: Future[Response] = status map { sta =>
      if (sta.statusCode == 404) {
        sta
      } else {
        Nil
      }
    }
    overallStatus map { os =>
      if (os.isEmpty) {
        Resposne(200)
      } else {
        os
      }
    }
  }
}

{% endhighlight %}

At the first glance, there are nothing particularly harmful about this approach, apart from it may be hard to read, slow to run and hard to maintain.

There is one show stopper though, the **testability**. Obviously someone who did that is either just trying to hack something together or simply can't give a toss. And I do find refactor when the code is difficult to test very helpful. Pulling bits of functionalities out to its own method or a new class is the benefit you do get from doing TDD, but the true challenge is to identify functionalities, think about the design and eventually refactor.

If you read in more detail, it is not hard to tell the controller is doing way to much on its own, a complete **violation of Single Responsibility Principle**. That is bad, because it means it costs a lot more to make a change to the code. Think about the things that can change here, the content for the request, the format of the content, or even the whole workflow might change as well. Refactor the code so that the classes have one and only one responsibility is the cure to this problem.

Echoing the previous two points, what we have actually achieved is the high **reusability** of the code.

{% highlight scala %}

import play.api.libs.ws._

class MyController(
  databaseManager: DatabaseManager,
  requestMaker: RequestMaker,
  responseParser: ResponseParser,
  requestProcessor: RequestProcessor
  ) extends Controller {
  def ping: Future[Response] = {
    val urls: List[String] = databaseManager.urls
    val response: Future[WSResponse] = requestMaker.send(urls)
    val status: Future[Response] = response map {
      res => responseParser.parse(res)
    }
    requestProcessor.getOverallStatus(status)
  }
}

class DatabaseManager {
  def urls = ???
}

class RequestMaker {
  def send(urls: List[String]): Future[WSResponse] = urls map { url =>
    WS.url(url)
      .withHeaders("Accept" -> "application/json")
      .withRequestTimeout(timeout)
      .get()
  }
}

class ResponseParser {
  def parse: Response = ???
}

class RequestProcessor {
  def getOverallStatus(status: Future[Response]): Future[Response] = {
    val overallStatus: Future[Response] = status map { sta =>
      if (sta.statusCode == 404) {
        sta
      } else {
        Nil
      }
    }
    overallStatus map { os =>
      if (os.isEmpty) {
        Resposne(200)
      } else {
        os }
      }
  }
}

{% endhighlight %}

Beyond the scope of this code, there are a couple of things that would be useful to think about when refactoring.

Starting with **design patterns**. Pretty much everyone agrees that there are common patterns in software engineering, and it is good to use them when appropriate. But why? Because it creates a common language to solving similar problems amongst developers. Refactoring your code to use common patterns is exactly how you make the code more readable and maintainable for others, and don't be shy in ripping others' code apart to apply patterns.

It is nice to adopt design patterns while the code is evolving, but what if the problem is so big yet so popular that obviously has been dealt with over and over again?

Like implementing MVC pattern in the application, why not use a **framework** rather than reinventing the wheel? It is worth noting that decisions to use a framework doesn't have to made in the beginning of the project, it can be introduced quite organically to solve a particular problem, such as using [Guice](https://github.com/google/guice) for dependency injection, or applying [mockito](http://mockito.org/) to assist testing.

Even so, using framework does not come without compromises, this is an 'All in or nothing' approach. What you are committing to is the way of thinking and the whole ecosystem behind it. So do choose frameworks wisely.
