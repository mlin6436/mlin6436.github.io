---
layout: post
title: "Resolving Merge Conflicts in Git"
tags: ["git", "merge", "conflict", "rebase"]
---

<div class="message">
Merging conflicts, as one of the most traumatising experience any dev can have, is the process we all seek to avoid.
But with the help of Git, everything can be done so elegantly yet painless.
</div>

# Relax, you can't make it worse

Yes, despite the fear and discomfort we all suffer on this topic, first thing to remember is that you literally can't make matters worse in Git.
As notorious as it can be like a [tree conflict](#) in SVN, Git has already taken care of a lot of funky scenarios, leaving you a peace of mind while merging.
Also, as a last resort, you always have the choice to undo a merge.

So [don't worry](http://www.youtube.com/watch?v=Oo4OnQpwjkc).

# How do conflicts happen?

A typical example could be the changes are made to the same part of code in the same file on two branches.

{% highlight yaml %}
origin a <--- b <--- c
  |                  ^HEAD
  |
 foo   a <--- b <----- d
{% endhighlight %}
When we try to merge 'foo' branch into 'origin' branch,

{% highlight bash %}
$ git checkout master
$ git merge foo
{% endhighlight %}

We would receive a lovely message saying 'CONFLICT', and the merge should fail miserably.

# How to deal with the conflicts?

Supposingly, you can go blame the other people an incompetent dev, or a complete nutcase.

{% include image.html url="/media/2014-08-11-resolving-merge-conflicts-in-git/blame-game.gif" width="100%" description="Blame game" %}

Since there is only [one in a million](http://www2.jpl.nasa.gov/sl9/back2.html) chance for merge conflicts to happen, I rather take this as an opportunity to unleash the power of dragon.

Open up the file with conflicts in [any editor](http://lifehacker.com/five-best-text-editors-1564907215) of your choice, you will see things like:

{% highlight yaml %}
<<<<<<< HEAD
a line in master branch, and should remain here.
=======
This is now a line in foo branch
>>>>>>> foo
{% endhighlight %}

Now, cleaning up the bits you don't want is really the matter of patience. What you've got left to do is to finish the merge by doing more or less like a normal commit.

{% highlight bash %}
$ git add <file>
$ git commit -m '<message>'
{% endhighlight %}

And you will actually have both commits from the two branches, and the HEAD is now sitting at the merge commit.

{% highlight yaml %}
origin a <--- b <--- c <--- d < ---- e
  |                       /          ^HEAD
  |                      /
 foo   a <--- b <----- d
{% endhighlight %}

# What if I make poo poo in the merge?

As promised, you can undo the merge from the point of merge <strong>up to</strong> committing the changes, by:

{% highlight bash %}
$ git merge --abort
{% endhighlight %}

The worst comes to the worst that you want to retract whatever merge is <strong>committed</strong>, you can still reset the repository to where it was before by:

{% highlight bash %}
$ git reset --hard <commit>
{% endhighlight %}

# There's more to come... Rebase
