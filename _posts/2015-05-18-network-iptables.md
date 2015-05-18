---
layout: post
title: "Control Network with iptables"
tags: ["network", "iptables", "linux", "raspberry pi"]
---

<div class="message">
This time, I get to have fun with configuring a [Raspberry Pi](https://www.raspberrypi.org/) with ([Raspbian](https://www.raspbian.org/) installed) to limit network accessibility so that it can meet our security compliances.
</div>

# Thought Process

The idea is simple, we want to limit the number and destinations of requests the Pi can send, and it should only receive data from trustable source(s), also the Pi should be not act as an IP server.

Because there are dashboard application running on the Pi, which requires access to various systems, the obvious option is to blackout all inessential network traffic by only allowing requests and responses between dashboard and the systems it monitors.

# iptables

With the direction clarified, I then set out to find what kind of tool can be used for this purpose. There are two options after a short investigation: [iptables](https://help.ubuntu.com/community/IptablesHowTo) and [ufw](https://help.ubuntu.com/community/UFW). Considering ufw is a wrapper around iptables, I went for the tough option, iptables. It is a Linux distribution likely to have pre-installed, but if not, you can always use the following command to get it.

{% hightlight bash %}
$ sudo apt-get install iptables
{% endhightlight %}

Just to get a flavour of what the command can do, try this command,

{% hightlight bash %}
$ sudo iptables -L
{% endhightlight %}

which should yield results like this,

{% hightlight bash %}
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
{% endhightlight %}

# A Bit of Background

From the output listed above, you can see there are three different type of chains:

- `Input` chain is used to control the behavior for incoming connections. Any data or connection sends to this server will go pass this step to match the sender IP address with the rules set in the chain.
- `Forward` chain is used for incoming connections that aren’t actually being delivered locally. This is used in the case of a router, the iptables will take care of the data sent to this server by forwarding it to the target destination.
- `Output` chain is used for outgoing connections. Before making any network request, such as `ping`, iptalbes will establish a check on the output to decide whether to turn down the connection or not.

For each type of chain, you can define rules on two different levels: `policy` and `connection-specific`. It is not hard to guess a policy is what is casted to the whole chain. While connection-specific rules can either be set individually or used as exceptions to the policy rules.

As part of the rule, there are three target options to choose to apply:
- `Accept` will allow the connection.
- `Drop` will drop the connection and act like as if nothing happened. This is great for the purpose of hiding the server from any source that tries to connect to it.
- `Reject` will turn down the connection and send back an error message. This can be used to reject the connection and notify the source that the server's firewall has actively blocked the connection.

# Command to Shut Them All Down

With the definitions out of the way, the purposes of following commands should be pretty much self-explanatory,

{% hightlight bash %}
$ sudo iptables -P INPUT DROP
$ sudo iptables -P FORWARD DROP
$ sudo iptables -P OUTPUT DROP
{% endhightlight %}

This is the first step of the network configuration, which I later find should be the `last` step of what I try to achive. The reasons are simple, I need to setup connection exceptions before shut everything out using the almighty policy rules.

# Command to Make Connection-Specific Rules

With iptables command switches listed [here](https://help.ubuntu.com/community/IptablesHowTo), I will only list an example of how to use it to create a connection exception,

{% hightlight bash %}
$ sudo iptables -I INPUT -s example.com -j ACCEPT
$ sudo iptables -I OUTPUT -d example.com -j ACCEPT
{% endhightlight %}

In the command, there are few little tricks to watch out for,

- `-I` means inserting the rule, and you can insert the rule to a specific position in the rule chain by `-I INPUT 5`, while `-A` can be used to append the rule to a rule chain.
- `-s` defines source specification, and `-d` defines destination information. Use them with the right rule chain.
- `-j` jumps to the specified target option, including `ACCEPT`, `DROP`, `REJECT` (as mentioned above) and `LOG`.
- `example.com` configuration can either be target `hostname` or `IP`, iptables is clever enough to resolve this when it is executed.

# Debugging and Tracing

To able to tell whether the rules work is important, an easy option for debugging is `ping`. A happy connection should give updates of data packages received from the hostname. For a blocked outbound policy `-P OUTPUT DROP`, you will see `ping: unkown host example.com`. For a blocked inbound policy `-P INPUT DROP`, you should expect the same message after a short period of time because of the timeout on receiving data.

When working on allowing connection exceptions in my application, of course it wouldn't work with only the codes above because of the convoluted nature of enterprise systems, where the simple request would bounce between different domains before it reaches the true server and gives the data back.

A more sophisticated way to trace what happens after a request is sent is via `dig` (available from 'sudo apt-get install dnsutils'). 

# What About `localhost`?

I am so glad you think about this, because this has caused me an unspeakable amount of pain. Despite the fact that I've got the rules working, while funny enough, the dashboard (running locally) just couldn't give me anything. By that, I mean no network requests in the debug console, either does the website loads. I think I should have figured it out a bit earlier, but just to save you the trouble stretching your head wondering why, these couple lines are what you need.

{% hightlight bash %}
$ sudo iptables -I INPUT -s 127.0.0.1/24 -j ACCEPT
$ sudo iptables -I OUTPUT -d 127.0.0.1/24 -j ACCEPT
{% endhightlight %}

# Script!

Yes, you probably have figured, it is easier to put the commands together in a script, not only for reusability sake, but also streamlines the process.

{% hightlight bash %}
#!/bin/bash

sudo iptables -I INPUT -s example.com -j ACCEPT
sudo iptables -I OUTPUT -d example.com -j ACCEPT

sudo iptables -I INPUT -s 127.0.0.1/24 -j ACCEPT
sudo iptables -I OUTPUT -d 127.0.0.1/24 -j ACCEPT

sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT DROP

sudo iptables -L -v
{% endhightlight %}

# More Script!

Yes, you are more than right. Not only you need a script to configure things, but also you need one to revert things when everything goes south.

{% hightlight bash %}
#!/bin/bash

sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT

sudo iptables -F

sudo iptables -L -v
{% endhightlight %}

# Persist the Changes

Here is a bit of tip I grab from internet: while the changes will stay, but only as long as you don't restart the iptalbes service. To avoid the holocaust of going back to stone age, here's what you need to do.

{% hightlight bash %}
$ sudo /sbin/iptables-save
{% endhightlight %}


# Advance Topics

### Block/Allow Traffic by Port
### iptables for IPv6

# Resources

http://www.cyberciti.biz/faq/linux-iptables-multiport-range/
https://www.linode.com/docs/security/firewalls/control-network-traffic-with-iptables
http://serverfault.com/questions/218707/iptables-rules-to-allow-http-traffic-to-one-domain-only
