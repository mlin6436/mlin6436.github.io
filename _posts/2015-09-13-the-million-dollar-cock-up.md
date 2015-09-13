---
layout: post
title: "The Million Dollar Cock-up"
tags: [""]
---

<div class="message">
When it comes to making a mess of IT systems, there is simply no bottom line. Amongst the places I've worked for, I have seem developers making cock-ups ranging from mis-calculation in accountant reports, deleting TBs of data from production database, and recently open sourcing GitHub repos with AWS user keys.
</div>

# In a Nutshell

With good faith in most of developers, it is unlikely anyone would be vicious enough to deliberately check in passwords or keys to GitHub and make it public. But developers are only humans, and humans do make mistakes. It is not hard to find a lot of AWS keys are checked in, and publicly available, even if you just do a quite simple [search](https://github.com/search?q=AWS_ACCESS_KEY&ref=searchresults&type=Code&utf8=%E2%9C%93) on GitHub.

While some of which we can get away with, some of which are just gonna cost you real money, like this dude's [500 dollar lesson](https://securosis.com/blog/my-500-cloud-security-screwup). And obviously he's not the only one, my team has recently received an email from Amazon:

{% include image.html url="/media/2015-09-13-the-million-dollar-cock-up/amazon.png" width="100%" description="Amazon Security Alert Email" %}

Ouch...

# How Did this Happened?

It starts from a good will from an ex-teammember we've never met and his dedication to open source a project he's created. It all sounds good until shit hits the fan.

{% include image.html url="/media/2015-09-13-the-million-dollar-cock-up/github_1.png" width="100%" description="Initial Check-in with AWS Credentials" %}

Just like any sensible developer, he soon spotted his mistake and corrected it by removing the sensitive information. But because of the unforgiving nature of version control, **whatever is checked-in remains in the Git history**. Hence why we received this Amazon email out from blue.

# Fire Fighting

As soon as the Amazon security email is received, it is a code red for us. As the security guy in the team, I soon draw out the actions:

> - Revoke the compromised key in AWS
> - Make the GitHub repo private for further investigation
> - Rotate the key to resume the system
> - Investigate policies associated with the user
> - Evaluate the impact and check for unauthorised usages

# Aftermath

Luck enough, the user which assigns the credential is only used for integration testing on a test account with limited permissions attached.

While the management won't be pleased with the implications to come, because this is not only a **security risk to our own account** which could lead to unexpected bills on that account, but also **violates the [AWS Customer Agreement](https://aws.amazon.com/agreement/)**, and that could mean anything from a warning to a serious fine.

I suppose it is too late to cry over spilled milk. But there are preventions and actions we could take on moving forward:

> - First and foremost, never check passwords or keys into source code!
> - If unfortunately sensitive information is committed, use [git filter-branch](https://help.github.com/articles/remove-sensitive-data/) to remove it before pushing the repo.
> - Lastly, use [AWS Temporary Credentials](http://docs.aws.amazon.com/AmazonS3/latest/dev/AuthUsingTempSessionToken.html) for running ad-hoc tasks.
> - More [resources](http://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html) from Amazon
