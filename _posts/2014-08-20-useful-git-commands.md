---
layout: post
title: "Useful Git Commands"
tags: ["git", "commands"]
---

<div class="message">
I have summarised some useful Git commands, grouped by common version control workflow in Git. If you are new to Git, going through the basics should be more than enough to get you started. On top of that, it is all about smoke and mirrors that every hardcore Git users could like to show off. So enjoy!
</div>

#Basics to get started

##Setup

For Mac, `brew install git`.

For Linux, `sudo apt-get install git` (you may try `sudo apt-get install git-core` in case of any problem) or `yum install git`.

For Windows, download the client [here](http://www.git-scm.com/) and install it.

There are also GUIs available from [GitHub](https://mac.github.com/) or [Tower](http://www.git-tower.com/), even though I tend not to use due to personal preferences.

##Create a repository

{% highlight bash %}
$ git init
{% endhighlight %}

##Copy a remote repository

{% highlight bash %}
$ git clone <repo>
{% endhighlight %}

##Stage changes

To add changes one by one.

{% highlight bash %}
$ git add <file>
{% endhighlight %}

Don't care, just add them all.

{% highlight bash %}
$ git add .
{% endhighlight %}

Some really really cool switch you should definitely try: patch add.

{% highlight bash %}
$ #TODO: I want to brag about this :)
$ git add -p
{% endhighlight %}

##Stash changes

Not ready to commit, or before re-synchronising your code with remote? Why not stash the changes before losing them?

It's dead easy.

{% highlight bash %}
$ git stash
{% endhighlight %}

You can always find everything here.

{% highlight bash %}
$ git stash list
{% endhighlight %}

And reapplying last change set is simple.

{% highlight bash %}
$ git stash pop
{% endhighlight %}

Unless you want to reapply a specified change set.

{% highlight bash %}
$ git stash pop <stash>
{% endhighlight %}

Or apply the changes, but also keep the stash in the stack.

{% highlight bash %}
$ git stash apply <stash>
{% endhighlight %}

##Commit changes

Happy with the changes? Commit them and give them a meaningful message. That is seriously not a question!

{% highlight bash %}
$ git commit -m "<message>"
{% endhighlight %}

##Commit logs

Like commit history of any version control system, but just more tricks you have got with Git.

To see the commits from a specific user.

{% highlight bash %}
$ git log --author=<user>
{% endhighlight %}

To see the files changed.

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

Want to be colleborative? You need to push your commits to the central repository so that your colleagues can see them.

{% highlight bash %}
$ git push
{% endhighlight %}

In case you haven't setup a remote repository for the current branch yet, hook it up with one.

{% highlight bash %}
$ git remote add origin <remote>
{% endhighlight %}

Then do the push to remote.

##Branch

It's important to create a branch to get everything organised for things like new feature, bugfix or release candidate. TODO: And I will put together a blog about Git workflow to explain this further.

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

To get the lastest code from remote repository.

{% highlight bash %}
$ git pull
{% endhighlight %}

##Merge and Rebase

After the update, you are likely to have some conflicts. To solve them, use merge, and Git will automatically integrate the changes to your branch.

{% highlight bash %}
$ git merge <branch>
{% endhighlight %}

Still faster than subversion huh.

Otherwise, use rebase to fast-forward (like a transplant) your branch changes onto feature history.

{% highlight bash %}
$ git checkout <feature>
$ git rebase <branch>
{% endhighlight %}

And you can't even do this in subversion.

A little case study just to see if you are still awake: synchronising a fork to its source.

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

If you just want the changes, but don't necessarily need the change history, merge is another option. Not recommended though.

{% highlight bash %}
$ git merge upstream/master
{% endhighlight %}

Push the new codebase to your fork.

{% highlight bash %}
$ git push origin master -f
{% endhighlight %}

##Tagging

Create a tag with a specific commit.

{% highlight bash %}
$ git tag <tag> <commit>
{% endhighlight %}

#The power of undoing

##Undo changes

To revert a single file.

{% highlight bash %}
$ git checkout <file>
{% endhighlight %}

To revert all changes.

{% highlight bash %}
$ git reset --hard HEAD
{% endhighlight %}

##Unstage changes

What happens if you put the wrong file into stage? Undo it.

{% highlight bash %}
$ git reset <file>
{% endhighlight %}

##Rewrite last commit message

Go edit the last commit message as you wish.

{% highlight bash %}
$ git commit --amend
{% endhighlight %}

##Undo commits

Revert a commit by creating a new commit to undo the last commit.

{% highlight bash %}
$ git revert <commit>
{% endhighlight %}

Rollback to a specific commit, and reserve changes.

{% highlight bash %}
$ git reset --keep <commit>
{% endhighlight %}

Abandon ship. Use with caution.

{% highlight bash %}
$ git reset --hard <commit>
{% endhighlight %}

NOTE: reset is similar to revert under the hood, the removed info is kept in git database for 30 days. And you can always unearth them.

##Delete a branch

Remove a branch locally.

{% highlight bash %}
$ git branch -d <branch>
{% endhighlight %}

And remotely.

{% highlight bash %}
$ git branch -dr origin/<branch>
{% endhighlight %}

#Some things to bear in mind

Git is a powerful tool, but it doesn't prevent you from shooting your own foot if you don't work under some principles. Therefore, in order to make everyone's life easier, it is useful to have some common sense when working with Git.

>Commit only related changes

>Write good commit messages

>Use branches extensively

>Never commit half-done work

>Never amend published commits
