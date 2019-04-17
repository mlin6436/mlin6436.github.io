---
layout: post
title: "About Commit Message"
tags: ["commit message"]
---

<div class="message">
There are a million ways to write your commit message, and there are a million ways to mess it up, and ths xkcd comic sums it up very nicely.
</div>

{% include image.html url="/media/2019-02-01-commit-message/git_commit_2x.png" width="100%" description="Git commits by xkcd" %}

Even though there's no correct answer to writing a commit message, there are principles to follow to make the commit messages more clear and readable, and people will really appreciate the effort put into making it nicer when tracing a bug or trouble shooting.

# A good commit message

A good commit message should look like this:

{% highlight bash %}
JIRA-101 feat: add a new icon to home page

- add icon
- update CSS styling to accommodate the new icon

Co-authored-by: Bob <bob@example.com>
{% endhighlight %}

The commit message can be broken down to the following parts:

{% highlight bash %}
<ticket-number> <semantic-tag>: <commit-message>

- <description1>
- <description2>

Co-authored-by: <participant1>
Co-authored-by: <participant2>
{% endhighlight %}

- __Ticket number__ gives clear indication what issue or feature it relates to.
- __Semantic tag__ is used to summarise the purpose of the commit.
- __Commit message__ is a detailed but brief message on what has been done in the commit.
- __Participants__ is normally used in a paring/mobbing session.

If this makes sense straight away, then you are good to go. But if you want more theory backing, carry on reading and I will do my best to explain.

# Small commits

You may be wondering why including semantic tag is a good idea. In order to understand why, let's establish that `small commits` are more welcomed for the following reasons:

- it gives clear indication of your thought process during development
- it helps readers/reviewers understand clearly what each commit does
- it is easier to cherry pick commits if needed
- it makes revert/rollback of certain changes easier, without having to undo all the work

# Semantic tag

On top of small commits, `semantic tag` is a crutch to help you identify what can go in as one commit. You can use the following principles to decide what semantic tag is the most appropriate:

- `feat`: new features
- `fix`: bug fixes
- `docs`: documentation
- `style`: formatting, styling, anything that does not change functionalities of production code
- `refactor`: refactoring production code
- `test`: test related commits, including refactors, but not style related
- `chore`: update build script, configuration, anything that does not change functionalities of production code

# Commit message

There are also a few guidelines to follow when writing a commit message body:

- Start the commit message with a verb in present tense and be brief and concise
- Limit your self to writing less than 50 characters for the commit message, and less than 70 characters in commit message body, check out this [50/72 rule](https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c)
- When pairing/mobbing, put the anticipants name and emails in the commit message body

These principles can optimise how commits look in GitHub, and the more you use it, you will find it a massive help to team collaboration.

Happy days!
