---
layout: post
title: "Response Headers Matter"
tags: []
---

<div class="message">
This time, I am going to entertain on the fact that, due to a wrong configuration in response header from one of our data suppliers, we had a lot of fun playing with HTTP requests and responses, and of course a bit of WTF moment when finding the truth about the issue.
</div>

# Start Light

The nature of internet communication can be materialised as two parts: `requests` and `responses`.

Request typically means a message you send to a server, which contains a collection of [information](http://rve.org.uk/dumprequest), including protocol, host, URL and some header information.

While response on the other side normally relates to a message you receive from a server, which includes a status code, content, along with some headers.

# Why Response Headers Matter

Consider this scenario, we were just told that one of our data suppliers have reconfigured their servers, which whenever the same request is made to the API, we get `302` status code. That presumably shouldn't affect our application from carrying on as usual.

After exhausted the ideas that the issue of not being to receive messages anymore is caused by client side caching, DNS caching, application rejecting status codes other than 20*. The problem actually lies with misconfiguration of the headers on the server side, as it indicates in the tomcat log file.

{% highlight bash %}
  Caused by: org.apache.http.HttpException: Unsupported Content-Coding: application/xml
  	at org.apache.http.client.protocol.ResponseContentEncoding.process(ResponseContentEncoding.java:98) ~[httpclient-4.3.3.jar:4.3.3]
  	at org.apache.http.protocol.ImmutableHttpProcessor.process(ImmutableHttpProcessor.java:139) ~[httpcore-4.3.2.jar:4.3.2]
  	at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:200) ~[httpclient-4.3.3.jar:4.3.3]
  	at org.apache.http.impl.execchain.RetryExec.execute(RetryExec.java:86) ~[httpclient-4.3.3.jar:4.3.3]
  	at org.apache.http.impl.execchain.RedirectExec.execute(RedirectExec.java:108) ~[httpclient-4.3.3.jar:4.3.3]
  	at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:186) ~[httpclient-4.3.3.jar:4.3.3]
{% endhighlight %}

If you have got an eagle eye, you probably have spotted the problem instantly. While it might look legit in the first glance, it really isn't. According to [mozilla's](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers) definite guide to HTTP headers, `Content-Coding` is simply not a thing. That really upsets `apache httpclient`.

Ah, bugger.

# HttpClient Is A Broad Church

While most browsers might accept this `minor` mistake, most HttpClient won't.

By the way, what is a `HttpClient`? It is a facilitator that takes in request, packages it up, sends it to the target server and gets the response back.

In fact, it is such a broad church that even though it hasn't got its own wiki page, it has a variety of implementations depending on what language you are talking about, like [this](https://github.com/apache/httpclient),  [this](https://msdn.microsoft.com/en-us/library/system.net.http.httpclient(v=vs.118).aspx) and [that](https://docs.python.org/3.1/library/http.client.html).

Don't be fooled by the simple looking Joe, as the internet grows, more features are constantly added to different implementations of HttpClient, such as `Async`, support for `WebSocket`, `HTTP` as well as `HTTPS` and so on.

# Getting It Right

HttpClient is a beast itself, it may be easy to use for simple tasks, it could also be a pain to work with and configure sometimes.

To start with, there are a couple of things to consider when constructing a request. Anything that is even slightly unacceptable will result in either a 40* or 50* status code in the response.

> Protocol: HTTP vs HTTPS
> Headers: all headers should be supplied to the specifications
> Host: it may be important to get the host domain correct for some HttpClient

Testing HttpClient may not be a treat. The rule of thumb would be not to rely on internet connection for the tests to work, sometimes it may just be a bit costly to create a local mock server to simulate the behaviour.

Therefore, for unit testing, mocking HttpClient and stubbing out the response, like the following Scala code using Mockito would be one of the solutions.

{% highlight scala %}
httpClient.execute(any[Request]).returns(Future(response))
{% endhighlight %}

As a food for thought, using `Captor` to test what kind of request is constructed can also be an option.
