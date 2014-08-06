---
layout: post
title: "Dive into Git"
---

<div class="message">
Everything I've learnt about Git, everything I've tried with Git and every little tips and hints for Git.
</div>

#Git Workflow

#Basics to Get You Started

#Something to Bear in Mind

When it comes to version control, there is no trivial thing. Because if you think about the audiences of the code, it could be your colleages, your manager or most likely, YOU. 

Assuming you write good code, but comes with abolutely diabolical commits, you are not the only one who suffers, but definitely the one who should be slapped on the face. 

Therefore, why don't we all make each other's life easiler by bearing some, more like common sense, in mind.

>• Commit only related changes

>• Write good commit messages

>• Use branches extensively

>• Never commit half-done work

>• Never amend published commits

#Useful Topics

###• Cheat sheet
[Git cheat sheet](http://www.git-tower.com/blog/git-cheat-sheet-detail/)

###• Reset or revert a specific file to last commit

{% highlight bash %}
$ git checkout <file>

# To reset the file to a specific commit
$ git checkout <commit-hash> <file>
{% endhighlight %}

###• Undo 'git add' a file

{% highlight bash %}
$ git reset HEAD <file>
{% endhighlight %}

###• Stash changes

{% highlight bash %}
# Stash current changes
$ git stash 

# List all stashes
$ git stash list 

# Apply a specific stash, if <stash> is blank, the last stash will be applied
$ git stash apply <stash>

# Same as 'apply', but will also clear out the stash being applied
$ git stash pop <stash>
{% endhighlight %}

###• Syncing a fork

{% highlight bash %}
# Add remote repository to git named 'upstream'
$ git remote add upstream <remote>

# Fetch remote repository
$ git fetch upstream

# Switch to master branch
$ git checkout master

# Merge upstream into master. Ideal if the repo has been cloned or you'd like preserve your commits
$ git merge upstream/master

# Rebase current master with upstream. It REWRITES everything, use with caution!
$ git rebase upstream/master

# Push the changes, '-f' force push is optional here
$ git push origin master
{% endhighlight %}


