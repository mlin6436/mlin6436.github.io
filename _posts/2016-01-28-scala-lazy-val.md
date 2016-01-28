---
layout: post
title: "Scala lazy val"
tags: [""]
---

<div class="message">
In Scala, there is a cool concept called lazy val along with the normal val and def keywords we normally see when defining a value. I hope to un-reveal some of the mists on what cases should lazy val be used over others.
</div>

# The Similar

`val`, `def`, `lazy val` all exist to produce a value in Scala. Jolly good, right :)

# The Different

To start with, values defined in `val` are evaluated as soon as the object is initialised and the value will be stored throughout the lifetime of the object. Also the value defined by `val` is immutable, it can't be changed throughout the lifecycle either.

`def` is used to define a method, just like the method in Java, it is evaluated every time it is called. As long as the type definition is satisfied, any value could be produced after its invocation.

Whereas `lazy val` gives a little bit more flexibility, which is its value is only evaluated when first accessed, then the result is kept since, but can never be reassigned.

# `val` vs `lazy val`

When an object is created, all `val` values are instantiated, which implies the resource is instantly allocated.

{% highlight bash %}
scala> class X { val x = { Thread.sleep(2000); 15 } }
defined class X

scala> class Y { lazy val y = { Thread.sleep(2000); 13 } }
defined class Y

scala> new X
res0: X = X@6842775d

scala> new Y
res1: Y = Y@387c703b
{% endhighlight %}

From the results in REPL, you can easily tell while `Y` is processed straight away, it took 2 seconds before `X` finishes instantiation. It is fine if this is indeed what you wanted.

> But to speed up the application, it might worth considering only evaluate some values when they are needed, instead of in the beginning. Situation like this, it is ideal to use  `lazy val`, which would save a lot of application initialisation time.

# `def` vs `lazy val`

So far, it may sound like `lazy val` is just an immutable version of `def`. Well, that's not the whole picture.

{% highlight scala %}
lazy val foo = { println("x"); 15 }
val begin = java.util.Calendar.getInstance().getTime().getTime()
for (a <- 1 until 1000) {
  foo
}
val end = java.util.Calendar.getInstance().getTime().getTime()
println(end - begin)
{% endhighlight %}

{% highlight scala %}
def bar = { println("y"); 13 }
val barBegin = java.util.Calendar.getInstance().getTime().getTime()
for (a <- 1 until 1000) {
  bar
}
val barEnd = java.util.Calendar.getInstance().getTime().getTime()
println(barEnd - barBegin)
{% endhighlight %}

There are two things you probably have noticed by running the two blocks of code:

- the print line in `lazy val` is only printed once, while the one in `def` is printed a thousand times
- value defined with `lazy val` executes more than twice as fast as the one defined with `def`

The two observations are intriguingly linked because of the nature of `lazy val`, which is a reference to start with, then turns into an immutable value and stays available throughout the process.

> Therefore, it is worth adopting `lazy val` over `def`, if the value to access will never change and is likely to be accessed more than once.
