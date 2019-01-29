---
layout: post
title: "Bootstrap Scala Project"
tags: ["scala", "bootstrap", "gradle", "sbt", "typesafe", "activator"]
---

<div class="message">
Good start is half of the success, while knowing how to start is even more important. In this article, I will introduce you to a few different ways to bootstrap a Scala project to get you started easily.
</div>

# Gradle

Unsurprisingly, Gradle is pretty much the golden standard for building JVM-based languages, including Scala. Once installed, what you need to run is `gradle init`. Then you can choose from a range of types of projects to generate, `scala-library` is one of them.

Wait, sounds a bit too convenient?

Yes, it is literally this convenient, and the code compiles and tests pretty damn fast as well, in comparision to other mechanisms I am going to talk about. The catch is: it is not the default approach if you go around asking people that uses Scala. As a consequence, you are limited to create simple Scala library project only. 

So what now?

# SBT

`sbt`, it's what you get if you google for Scala build tool. While `sbt` stands for "Simple Build Tool", but it is not that simple, ironically. There's even a [book](https://www.oreilly.com/library/view/sbt-in-action/9781617291272/) about `sbt` (that's how simple it is).

Despite not the simplest build tool in the world, it actually does what it's designed to do: building Scala projects, and you should really invest in learning it because it's THE tool for building Scala projects natively, and more, which I won't elaborate here.

Going back to bootstraping, run `sbt new <template>` to create a templated Scala project. A list of supported templates can be found [here](https://www.scala-sbt.org/release/docs/sbt-new-and-Templates.html).

# Activator

Similar to `sbt`, `activator`, which is short for `typesafe-activator`, is created by Lightbend, which was founded by [Big Martin](https://en.wikipedia.org/wiki/Martin_Odersky). The reason for the confusing naming is because Big Martin's company used to be called "Typesafe", which got renamed to "Lightbend" around 2016.

`activator` is a wrapper around `sbt`, and also offers a rich set of Lightbend and third party Scala project templates to bootstrap a project, such as "akka", "finatra", "play", "spark" and etc, just to name a few.

You can install it conveniently via `brew install typesafe-activator`. And it's pretty simple to use as well, run `activator`.

# Conclusion

It is worth bearing in mind that `activator` will bootstrap the project based on however it's templated, and some will even be using `gradle` instead of `sbt`. Despite the complication, I do think it's a good tool to have in the toolbox, and can be used to access a wide range of templates, but definitely use `sbt` for the actual building of Scala project if you want a better experience.