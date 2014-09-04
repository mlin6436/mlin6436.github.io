---
layout: post
title: "Dive into Git"
tags: ["git", "commands"]
---

<div class="message">
I have summarised some high usage Git commands, grouped by common Git workflow. If you are new to Git, going through the basics should be more than enough to get you started. On top of that, it is all about smoke and mirrors that every hardcore Git users would like to show off. So enjoy!
</div>

#Basics to get started

##1. Setup

For Mac, `brew install git`.

For Linux, `sudo apt-get install git` (you can try `sudo apt-get install git-core` if Git is not installed) or `yum install git`.

For Windows, download the bash client [here](http://www.git-scm.com/) and install it, and you get bash commands for free, happy days!

Even though I tend to avoid GUIs cause they are confusing, inefficient and most importantly not hipster, you can still find something from [GitHub](https://mac.github.com/) or [Tower](http://www.git-tower.com/).

##2. Create a repository

Easy peasy.

{% highlight bash %}
$ git init
{% endhighlight %}

##Copy a remote repository

You can copy either from a server or most likely GitHub, you can have a quick scan through [this](https://gist.github.com/grawity/4392747) to decide which remote URL to use.

{% highlight bash %}
$ git clone <repository>
{% endhighlight %}

I personally use SSH because I used to work behind a corporate proxy.

##Stage changes

Before commit, you need to group your changes and add them into stage.

To add files one by one.

{% highlight bash %}
$ git add <file>
{% endhighlight %}

Or don't care, just add them all.

{% highlight bash %}
$ git add .
{% endhighlight %}

Some really cool [stuff](http://nuclearsquid.com/writings/git-add/) you should try with stage.

{% highlight bash %}
$ git add -p
{% endhighlight %}

##Stash changes

Not ready to commit, or before re-synchronising your code with remote and don't want to lose your local changes? Stash them.

It does what it says, to keep your things away from destruction.

{% highlight bash %}
$ git stash
{% endhighlight %}

You can always find the things you stashed here.

{% highlight bash %}
$ git stash list
{% endhighlight %}

And applying last change set back to codebase.

{% highlight bash %}
$ git stash pop
{% endhighlight %}

Or applying a specified change set.

{% highlight bash %}
$ git stash pop <stash>
{% endhighlight %}

Unless you want to both apply a change set and keep the stash in the stack.

{% highlight bash %}
$ git stash apply <stash>
{% endhighlight %}

##Commit changes

Happy with the changes? Commit them and give them a meaningful message. And I seriously mean [it](http://stackoverflow.com/questions/43598/suggestions-for-a-good-commit-message-format-guideline)!

{% highlight bash %}
$ git commit -m "<message>"
{% endhighlight %}

##Commit logs

Like showing commit history in any version control system, Git is just more powerful in every single way.

To see the commits from a specific user.

{% highlight bash %}
$ git log --author=<user>
{% endhighlight %}

To show the files changed.

{% highlight bash %}
$ git log --name-status
{% endhighlight %}

To view a compact one liner commit history.

{% highlight bash %}
$ git log --pretty=oneline
{% endhighlight %}

Even more crazy things you can do, like displaying an ASCII art tree commits decorated with tags and branches.

{% highlight bash %}
$ git log --graph --oneline --decorate --all
{% endhighlight %}

##Push changes

So far it is all great, but only in your machine. If you want to be colleborative, push your commits to the central repository so that your colleagues can [criticise](http://dilbert.com/strips/comic/2011-01-14/) them.

{% highlight bash %}
$ git push
{% endhighlight %}

In case you haven't setup a remote repository for your current branch yet, hook it up with one.

{% highlight bash %}
$ git remote add origin <remote>
{% endhighlight %}

Then do the push again.

##Branch

It's important to get codebase organised using dedicated branches for stuff like new feature, bugfix or release candidate. [This](http://nvie.com/posts/a-successful-git-branching-model/) has been proven to be a very successful branching model for the majority.

Branching in Git is fast. Faster than subversion.

{% highlight bash %}
$ git branch <branch>
{% endhighlight %}

To list all the branches.

{% highlight bash %}
$ git branch -va
{% endhighlight %}

To switch to a branch and start working.

{% highlight bash %}
$ git checkout <branch>
{% endhighlight %}

This is a short hand for creating a branch, switching to it, and bring all unstaged changes with you.

{% highlight bash %}
$ git checkout -b <branch>
{% endhighlight %}

Want to share your branch?

{% highlight bash %}
$ git push -u origin <branch>
{% endhighlight %}

`-u` is to establish a tracking relationship between a branch and its remote branch

##Update

It is a good idea to get a cup of tea in the morning, and update your code!

{% highlight bash %}
$ git pull
{% endhighlight %}

##Merge and Rebase

And because you haven't done what I suggested above often enough, you are more than likely to end up with conflicts after a short while.

Keep calm and use merge, and Git will automatically integrate the changes for you.

{% highlight bash %}
$ git merge <branch>
{% endhighlight %}

And this is still faster than subversion, yet another win for Git.

Alternatively, use rebase to fast-forward your branch.

{% highlight bash %}
$ git checkout <master>
$ git rebase <branch>
{% endhighlight %}

{% include image.html url="/media/2014-08-20-useful-git-commands/git-rebase.png" width="100%" description="Git rebase works like a transplant" %}

Be aware that rebase will replace current branch's commit history with the other. And that is why a timely update is so important to avoid situation like this.

A little case study just to see if you are still awake: how to synchronise a fork with the source of truth.

First add the source repository as an upstream.

{% highlight bash %}
$ git remote add upstream <remote>
{% endhighlight %}

Then fetch all its content.

{% highlight bash %}
$ git fetch upstream
{% endhighlight %}

Switch to master branch, and rebase the master with upstream.

{% highlight bash %}
$ git remote add upstream <remote>
$ git rebase upstream/master
{% endhighlight %}

If you just want the changes, but don't necessarily need the change history, merge is another option. I would not recommend this as it could cause more pain later.

{% highlight bash %}
$ git merge upstream/master
{% endhighlight %}

Push the new codebase to your fork.

{% highlight bash %}
$ git push origin master -f
{% endhighlight %}

##Tagging

Tag a specific commit for release.

{% highlight bash %}
$ git tag <tag> <commit>
{% endhighlight %}

#The power of undoing

Use with caution.

##Undo changes

Before changes are staged, you can revert a single file.

{% highlight bash %}
$ git checkout <file>
{% endhighlight %}

Or revert all of them.

{% highlight bash %}
$ git reset --hard HEAD
{% endhighlight %}

##Undo stage

What happens if you put the wrong file into stage, or don't want it to be part of the commit?

{% highlight bash %}
$ git reset <file>
{% endhighlight %}

##Undo commits

Revert a commit by creating a new commit to rollback last commit.

{% highlight bash %}
$ git revert <commit>
{% endhighlight %}

Rollback to a specific commit, and reserve changes.

{% highlight bash %}
$ git reset --keep <commit>
{% endhighlight %}

Abandon ship.

{% highlight bash %}
$ git reset --hard <commit>
{% endhighlight %}

Reset is similar to revert under the hood, the removed info is still kept in git database for 30 days. So you can always unearth them if you want.

##Rewrite last commit message

Wrote something inappropriate? Rewrite it. Limited to last commit only.

{% highlight bash %}
$ git commit --amend
{% endhighlight %}

##Delete a branch

Remove a branch locally.

{% highlight bash %}
$ git branch -d <branch>
{% endhighlight %}

And remotely.

{% highlight bash %}
$ git branch -dr origin/<branch>
{% endhighlight %}

#It's all about Flexibility

Git is highly configurable in itself.

{% highlight bash %}
$ git config --list
{% endhighlight %}

And indeed whatever is listed here and more, you can adjust it based on your own preferences.

If you have an extremely small terminal, typically less than 72 characters wide, your display will be truncated like following (left) before [2.1.0](https://raw.githubusercontent.com/git/git/master/Documentation/RelNotes/2.1.0.txt).

{% include image.html url="/media/2014-08-20-useful-git-commands/git-truncation.png" width="100%" description="Git log displayed with truncati (left) and normal (right)" %}

You don't have to wait for the 2.1.0 to able to display a wrapped log like the example to the right. Even though `git log` uses `less -S` by default, it doesn't mean you can't make it more user friendly (as to the right):

{% highlight bash %}
$ git config core.pager "less"
{% endhighlight %}

#Some things to bear in mind

Git is a powerful tool, but it doesn't prevent you from shooting your own foot if you don't work under some basic principles. In order to make everyone's life easier, it is useful to have some common sense when working with Git.

>Commit only related changes

>Write good commit messages

>Use branches extensively

>Never commit half-done work

>Never amend published commits
