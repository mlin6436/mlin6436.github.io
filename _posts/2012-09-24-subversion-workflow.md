---
layout: post
title: "Setup a Subversion Workflow"
tags: ["subversion", "server", "version control", "tortoisesvn", "visualsvn"]
---

<div class="message">
In an multi-developer environment, version control turns out to be a very important thing to do before crack on coding.
</div>

There are certain options while setting up svn repository:

- Use public hosting service, e.g. google code
- Set up local repository
- Set up network share

By comparing the usability and impact, having a local network repository seems to be more feasible choice for most business.

> NOTE: This is not so true when I am re-editting this blog in 2014, despite the fact subversion still works nicely, everyone is encouraged to take on GitHub. It's not just because of the awesome free cloud service GitHub provides, but also because the better workflow with Git, the openness of code, the ease of integrating with CI.

Even though dealing with day-to-day check in check out is an easy task using [TortoiseSVN](http://tortoisesvn.net/), to set up a network share svn repository requires a little bit more than that. That's why [VisualSVN Server](http://www.visualsvn.com/server/) came to the rescue.

The step-by-step guide of setting up network share repository is as follow:

# Server-side

- Download and install [VisualSVN Server](http://www.visualsvn.com/server/download/)
- Open Visual SVN Server, choose `Create New Repository` to create a repository with the name you want
- Choose 'Create default structure' if you are used to trunk and branches structure

Till now it is all ready to go.

# Client-side

- Download and install [TortoiseSVN](http://tortoisesvn.net/downloads.html)
- Right click on any project folder which is ready to be checked in as a trunk
- Select `TortoiseSVN` -> `Import...`
- Type in the url of the svn server just created, and make sure put in some [good comments](http://programmers.stackexchange.com/questions/52267/why-should-i-write-a-commit-message) before check in
- The project will be created and imported to svn server after clicking 'OK'
- To check out the code on another device, again you will need some sort of svn client installed, and then select `SVN checkout` given the svn server url

And that's all you need to do to setup a svn server and happy check in!

# References

[Why should set up network sharing repository](http://tortoisesvn.net/docs/release/TortoiseSVN_en/tsvn-serversetup.html)

[A step-by-step guide video of server SVN setup](http://www.youtube.com/watch?v=yGIo9_x-YSo)

[A step-by-step guide of server SVN setup](http://rajibmahmud.wordpress.com/2009/01/09/work-with-visual-svn-server-tortoise-svn/)
