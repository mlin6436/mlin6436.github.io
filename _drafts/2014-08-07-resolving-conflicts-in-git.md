---
layout: post
title: "Resolving Merge Conflicts in Git"
tags: ["git", "merge", "conflict", "rebase"]
---

<div class="message">
For me, merging conflicts is as torturing as sitting beside a time bomb with no idea when it's gonna go off. As for the majority of devs, depending on experience and knowledge of the codebase, it could be better or worse. But sure thing is that, nobody enjoys when it happens, not to mention if he is the one who is responsible for fixing it.
</div>

#Relax, You Can't Make It Worse

Despite the fear and discomfort we all suffer on this subject, it is reassuring to know that you actually can't make it worse. Why? The way Git works out the merge is completely different from SVN.

#The Subversion Approach

Considering this scenario:

{% highlight yaml %}
trunk 1 ---> 2 ----> 4 ---> 6
              \
               \
foo             3 ----> 5
{% endhighlight %}

To make the merge from branch foo to trunk, you first have to checkout trunk, and run the command:

{% highlight bash %}
svn merge -r 3:5 <foo-url>
{% endhighlight %}

figures crossed there is no conflicts and all tests run without an issue, you then need to check the new code back into trunk.

{% highlight yaml %}
trunk 1 ---> 2 ----> 4 ---> 6 ---> 7
              \                   /
               \                 /
foo             3 -----------> 5
{% endhighlight %}

Now in revision 7 we have the lastest and greatest code, which sounds great right? Well, the extra merge meta is only available given that you are on SVN 1.5 or later, so good luck figure out what's got merged what's not when codebase expands. Moreover, if you've got more than one line of code in play, the situation will soon get more and more nasty like following.

{% highlight yaml %}
bar                     5
                       / \
                      /    \
trunk 1 ---> 2 ----> 4 ----> 7
              \             /
               \           /
foo             3 ------> 6
{% endhighlight %}

To be fair, there really is nothing wrong with this approach, and it works as sweet as a nut if you do know what you are doing and take good care of your codebase.
But there are significant drawbacks of this approach as well:
• the amount of time required to figure all merges out is definitely worse than linear time.
• when merging, you are literally dealing with two hard copies of the code
• [other facts as well???]
And this is all because svn is based on the tree structure.

#The Git Approach

To the contrast, Git uses [graph](http://en.wikipedia.org/wiki/Directed_acyclic_graph) to represent the version structure.
And the real benefit of a directed acyclic graph is that each commit has one or more parent references:

{% highlight yaml %}
origin a <--- b <--- c
  |                  ^master
  |
local  a <--- b <--- c
                     ^master
                     ^origin/master
{% endhighlight %}



#So Why Git Is Better?

•The Power To Undo

#Rebase!?
