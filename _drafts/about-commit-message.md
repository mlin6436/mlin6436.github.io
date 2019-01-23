About Commit Message

I've been to many places and have seen a lot of different ways of writing a commit message. Some of them good, some of them bad, some of them confusing. But that's not the point and there’s no need for any one to be named and shamed, I believe this is par of journey that as a community we all have to take part and contribute, and find out what works best for ourselves.

First thing, I’d like to talk about why is commit message important. Look at this xkcd comic, you will get it.

[image]

Of course, there’s no one/system stop you from putting rubbish in your commit message, but it doesn’t mean you should not care about it. In fact, commit message is a very important part of a development cycle, it not only gives notion of what you were doing at a point in time, but also serves as critical documentation for issue tracing (or blaming if you’d like to see it that way).

Regardless how many different styles of commit message I’ve used, I believe there’s a common ground for all the good/working commit message, which is in this format:

{% highlight bash %}
<ISSUE-NUMBER> <Semantic Tag>: <Actual commit message>
{% endhighlight %}

There are a couple of concepts worthing mentioning in support of this commit message style:

- Small commits: we all like small commits, it’s focused on your task at hand, it is easy to cherry pick if needed, it demonstrates your thought process during the work
- Small tickets: this ties into a bigger topic which I’d like to talk about, but the idea is simple. We are terrible in dealing with massive systems, not to mention implementing it in one go. The logical way is to break the problem (an EPIC) into small achievable tasks. You may be wondering what’s “achievable”. We typically use techniques like estimation to get a good idea of how big a task is. In simple words, smaller the deliverables are, easier for it to be delivered in time in budget.

Once we have accepted the basic principles, we can then see a commit message as a tagging system.

The `ISSUE-NUMBER` part gives indication of which issue/task we try to resolve. I would prefer having the issue/ticket number attached to the commit message, but I don’t necessarily mandate it, because if you are using feature branch during development, and the feature branch is named after the issue/ticket, you may consider this redundant.

`Semantic Tag` is an interesting take on clarifying the purposes of a step taken in the task, it would be refactor, test or even doing some chores (here’s a good reference to it https://seesparkbox.com/foundry/semantic_commit_messages). This both fits in the cycle of TDD (test, dev, refactor) nicely, but also shows your thought process, and can serve an important role in helping reviewers understand.

There are a couple of notes as well:

- Start the commit message with a verb and cut unnecessary chatty words
- 50/72 rule [https://medium.com/@preslavrachev/what-s-with-the-50-72-rule-8a906f61f09c]: 
- The commit message shouldn’t be longer than longer than 50 characters, and that has GitHub to blame for not being able to display longer messages nicely.
- If you genuinely need a large commit, and want to be more descriptive about the milestones, it’s preferred to list the milestone in the commit message body

{% highlight bash %}
<ISSUE-NUMBER> <Semantic Tag>: <Actual commit message>
- Milestone 1
- Milestone 2
{% endhighlight %}

At this point, you may think it’s nice and great, hold your thought, there’s something better. If by any chance you are trying out pairing/mobbing in your team, and all the heads should be held accountable for the damages done, you can use “co-author” in your commit message to tag the partners in crime.

{% highlight bash %}
git commit -m "Refactor usability tests.
>
>
Co-authored-by: name <name@example.com>
Co-authored-by: another-name <another-name@example.com>"
{% endhighlight %}
Ì
And GitHub can actually display them

Happy times.
