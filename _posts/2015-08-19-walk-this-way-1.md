---
layout: post
title: "Walk This Way, Ep.1"
tags: ["scala", "play", "activator", "dashboard project", "continuous delivery", "framework", "scala", "bbc", "play"]
---

<div class="message">
This time, I am going to use a series of blogs, based on a 2-month project my team has done in Platform in the BBC, to showcase a couple of good engineering practices, some bits and bots about using <a href="http://www.scala-lang.org/">Scala</a> and <a href="https://playframework.com/">Play</a> framework, and reflect on what we could have done better.
</div>

# In the Beginning

My team needs a dashboard, and the reason why we need it so urgently is: we have taken over many products in the past few months and the means of keeping our tabs on them are so divided.

There are currently a few ways to monitor a cloud product in the BBC:

> - monitoring the built-in status endpoints in the product
> - [CloudWatch](http://aws.amazon.com/cloudwatch/) alarms and the email notifications
> - [Kibana](https://www.elastic.co/products/kibana) log aggregation and interrogation
> - 24/7 Operations team actively monitors all products using [Zenoss](http://www.zenoss.com/)

So why are we still paranoid?

> - there isn't a visual way to see what is going on in our products
> - we don't have real control of our products apart from relying on CloudWatch or operations team telling us something's gone wrong
> - we are not able to identify and solve problems in early stages

Most importantly, we have a reputation to maintain, our **SLAs** commitment. This dashboard should really help us understand the live status of our products in a visual way using a traffic light system.

# Whiteboard Session

To solve our problem, we chained ourselves up in a room as a team, brain storming design and architecture of the product, identifying dependencies and picking preferable technologies. And surprisingly, we came to a consensus without much of debate.

{% include image.html url="/media/2015-08-19-walk-this-way-1/whiteboardsession1.jpg" width="100%" description="Design from one squad, where do they find the green and red markers!?" %}

{% include image.html url="/media/2015-08-19-walk-this-way-1/whiteboardsession2.jpg" width="100%" description="Design from my squad, and we were really thinking far ahead ;)" %}

# A Common Ground

There are a selection of frameworks and technologies we were really keen on, and best represent the diverse experience of our team: [Dashing](http://dashing.io/) (Ruby), [Dashing.js](https://github.com/fabiocaseri/dashing-js) (Js), [Play](https://playframework.com/) (Scala), [Scalatra](http://scalatra.org/) (Scala) and [Spark](http://sparkjava.com/) (Java).

The intension was to build something we can use, but also taking the opportunity for everyone to get familiarised with Scala. For this reason, two frameworks stood out after we built a couple of proof of concepts are: Play and Scalatra.

Scalatra is a barebone framework, which provides almost nothing but routing. It is the minimal way of producing API or website from scratch. But after two rounds of intense voting, we chose Play instead. Because as our first Scala project to be rolled out to production, we could use a bit of hand holding, such as the routing support, configurable settings, MVC boilerplate and built-in [testing framework](https://etorreborre.github.io/specs2/) provided in Play.

# Forming, Storming, Norming and Performing

You might already be wondering what are all these fuss about, use a ready made template, and save yourselves all the trouble.

Yes, we could.

But as a young team only came to existence for 3 months, with everyone coming from different background with few common understandings on Scala (which is used a lot in our products), this project is perfect for us to learn to work together. Just like [Tuckman's Team Developing Strategy](https://en.wikipedia.org/wiki/Tuckman%27s_stages_of_group_development), we are in the beginning of the four stages, and throughout this series of articles, you will be able to find our how we have integrated as a team as well as our struggles. More on that later.

# Continuous Delivery

After agreeing on the design of dashboard and the technologies to use, the most important thing we need to think about is: how are we going to build something people want to use?

Despite being in a startup or a big enterprise, it is not hard to find examples that people throw in 120% of effort on a project, but end up with virtually no appreciation for what they've created at all. The similarity of all those unsuccessful stories are: keeping the product development in the dark, and delivering it in one big bang release and hoping it will become a hit. It sounds insane when being pointed out, but haven't we all more or less done things like that before.

To move away from this pattern of failure, **continuous delivery** comes to the rescue, it gives certain advantages over the old model:

> - work in short iterations
> - do small releases and release your product often
> - gather and react to feedbacks faster
> - people get used to the release process and not afraid of doing releases
> - better transparency, scalability and availability of the product

Despite the benefits continuous delivery can bring, it is a big step forward in terms of way of working:

> - it requires a complete change of mindset
> - it requires vertically slicing user stories so that it is small and deliverable
> - it requires good test coverage to ensure the code quality
> - it requires an efficient continuous integration and deployment pipeline
> - it requires a hell lot of confidence from both business and engineers

# To Be Continued

Next time, I will give more insight into how we renovated the process to be more lean and be able to do continuous delivery, and the rationales of why we **walk this way**.

At the meantime, if you find this article interesting, feel free to share it. Or if you have questions or suggestions, please leave a comment, or tweet me on [twitter](https://twitter.com/mlin6436) or email it to [mlin6436@gmail.com](mlin6436@gmail.com), I will get back to you as soon as I can.
