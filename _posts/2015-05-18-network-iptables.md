---
layout: post
title: "Control Network Traffic with iptables"
tags: ["network", "iptables", "linux", "raspberry pi"]
---

<div class="message">
This time, I get to play with <a url="https://www.raspberrypi.org/">Raspberry Pi</a> (<a url="https://www.raspbian.org/">Raspbian</a>) to limit its network accessibility to meet our security compliances.
</div>

# Thought Process

The idea is simple, we want to limit the number and destinations of requests the Pi can make, and it should only receive data from trusted source(s), also the Pi should not act as an IP server.

Meanwhile, because we need to run a dashboard application on the Pi which requires access to various systems, the obvious option is to blackout all inessential network traffic by only allowing requests and responses between Pi and the listed systems.

# iptables

After a short investigation, there are two tools at our disposal [iptables](https://help.ubuntu.com/community/IptablesHowTo) and [ufw](https://help.ubuntu.com/community/UFW). Considering ufw is only a wrapper around iptables, I went for the tough option, iptables.

It is a Linux distribution likely to have been pre-installed, you can always use the following command otherwise.

{% highlight bash %}
$ sudo apt-get install iptables
{% endhighlight %}

Just to quickly get a flavour of what `iptables` is capable of, try this command,

{% highlight bash %}
$ sudo iptables -L -v
{% endhighlight %}

which should yield results like,

{% highlight bash %}
Chain INPUT (policy ACCEPT 58533 packets, 58M bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination

Chain OUTPUT (policy ACCEPT 44797 packets, 5414K bytes)
 pkts bytes target     prot opt in     out     source               destination
{% endhighlight %}

# A Bit of Background

### Chain

From the output listed above, you can see there are three different type of chains:

- `Input` chain is used to control the behavior for incoming connections. A good use case would be managing `ssh` connections.
- `Forward` chain is used for incoming connections that aren’t actually being delivered locally. This is used in the case of a router, the iptables will take care of the data sent to this server by forwarding it to the target destination.
- `Output` chain is used for outgoing connections. Before making any network request, such as `ping`, iptalbes will establish a check on the output to decide whether to turn down the connection or not.

### Policy and Connection-Specific

For each type of chain, you can define rules on two different levels: `policy` and `connection-specific`. It is not hard to guess a policy is what is applied to the whole chain. While connection-specific rules can either be used as exceptions to policy rules or individually.

### Target

As part of the rule, there are three target options to choose from:

- `Accept` will allow the connection.
- `Drop` will drop the connection and act like as if nothing happened. This is great for the purpose of hiding the server from any source that tries to connect to it.
- `Reject` will turn down the connection and send back an error message. This can be used to reject the connection and notify the source that the server's firewall has actively blocked the connection.

# Command to Shut Down All Connections

With the definitions explained, the purposes of following commands should be pretty much self-explanatory,

{% highlight bash %}
$ sudo iptables -P INPUT DROP
$ sudo iptables -P FORWARD DROP
$ sudo iptables -P OUTPUT DROP
{% endhighlight %}

This is the first step of the network configuration, which I later find should in fact be the `last` step of what I try to achive. The reason is simple, I need to setup connection exceptions before shut everything out using the almighty policy rules.

# Command to Make Exceptions

With iptables command switches listed [here](https://help.ubuntu.com/community/IptablesHowTo), I will only list an example of how to use it to create connection exceptions,

{% highlight bash %}
$ sudo iptables -I INPUT -s example.com -j ACCEPT
$ sudo iptables -I OUTPUT -d example.com -j ACCEPT
{% endhighlight %}

In the command, there are few bits worth explaining,

- `-I` means inserting the rule, and you can even insert the rule to a specific position in the rule chain with `-I INPUT 5`. While using `-A` will append the rule to the end of a rule chain.
- `-s` defines source specification, and `-d` defines destination information. Configure them in the right rule chain.
- `example.com` configuration can either be target `hostname` or `IP`. iptables is clever enough to resolve a hostname when it is executed, so always use a hostname if possible.

# Debugging and Tracing

### ping

To able to tell whether the rules work is important, an easy option for debugging is `ping`.

A happy connection should give updates of data packages received from target.

{% highlight bash %}
PING example.com (93.184.216.34) 56(84) bytes of data.
64 bytes from example.com (93.184.216.34): icmp_seq=1 ttl=55 time=93.3 ms
{% endhighlight %}

When outbound policy is set to `-P OUTPUT DROP`, you will see,

{% highlight bash %}
ping: unknown host example.com
{% endhighlight %}

For inbound policy set to `-P INPUT DROP`, you should expect the same message after a short period of time, due to timeout whilst receiving data.

### dig

The convoluted nature of enterprise systems means the configuration is never as simple as in the example, where a simple request would bounce between different domains before it reaches the true server and gives the data back.

A more sophisticated way to trace what happens after a request is made is via `dig`.

{% highlight bash %}
$ sudo apt-get install dnsutils
{% endhighlight %}

An example of informaion trace from `dig example.com` is,

{% highlight bash %}
; <<>> DiG 9.9.5-3ubuntu0.2-Ubuntu <<>> example.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24517
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;example.com.			IN	A

;; ANSWER SECTION:
example.com.		21600	IN	A	93.184.216.34

;; Query time: 20 msec
;; SERVER: 127.0.1.1#53(127.0.1.1)
;; WHEN: Mon Jan 01 00:00:00 BST 1641
;; MSG SIZE  rcvd: 56
{% endhighlight %}

What is interesting is not the manually anonymised time, but the `ANSWER SECTION` and `SERVER`, which give you a good idea of how many places your request has been or possibly has been.

As part of exception rules setup, you will need to white list these IPs or Hostnames (preferred) in the rules, so that the information can reach whereever it needs to go.

# What About `localhost`?

I am so glad you think about this, because this has caused me an unspeakable amount of pain.

Despite the fact that I've got the rules working. Funny enough, the application (runs locally from 127.0.0.1) just couldn't give me anything. By that I mean no network requests in the debug console, either does the website loads. I think I should have figured it out a bit earlier giving how obvious it is, but just to give you a hint. Yes, my localhost is still blocked!

{% highlight bash %}
$ sudo iptables -I INPUT -s 127.0.0.1/24 -j ACCEPT
$ sudo iptables -I OUTPUT -d 127.0.0.1/24 -j ACCEPT
{% endhighlight %}

By doing this, you will be able to take back the control and launch application from local. Another way to do this is by using loopback, but I couldn't get it working in the way people suggested, so I am just listing it out for argument sake.

{% highlight bash %}
$ sudo iptables -I INPUT 1 -i lo -j ACCEPT
{% endhighlight %}

# Script!

Yes, you probably have figured, it is easier to put the commands together in a script, not only for reusability sake, but also streamlines the process. So here you go,

{% highlight bash %}
#!/bin/bash

sudo iptables -I INPUT -s example.com -j ACCEPT
sudo iptables -I OUTPUT -d example.com -j ACCEPT

sudo iptables -I INPUT -s 127.0.0.1/24 -j ACCEPT
sudo iptables -I OUTPUT -d 127.0.0.1/24 -j ACCEPT

sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT DROP

sudo iptables -L -v
{% endhighlight %}

# More Script!

And yes, you are more than just right. Not only you need a script to configure things, but also you need one to revert things when it goes south.

{% highlight bash %}
#!/bin/bash

sudo iptables -P INPUT ACCEPT
sudo iptables -P FORWARD ACCEPT
sudo iptables -P OUTPUT ACCEPT

sudo iptables -F

sudo iptables -L -v
{% endhighlight %}

# Persist the Changes

The changes will only stay as long as you don't restart the iptalbes service. To avoid the holocaust of being sent back to stone age, here's what you can to do to keep the changes safe and restore it if needed.

{% highlight bash %}
$ sudo iptables-save > /etc/iptables.rules
$ sudo iptables-restore < /etc/iptables.rules
{% endhighlight %}


# Advance Topics

Here are some more topics I'd like to cover over time when I get more experienced in this area.

- Block/Allow Traffic by Port
- iptables for IPv6

# Resources

http://www.cyberciti.biz/faq/linux-iptables-multiport-range/
https://www.linode.com/docs/security/firewalls/control-network-traffic-with-iptables
http://serverfault.com/questions/218707/iptables-rules-to-allow-http-traffic-to-one-domain-only
