---
layout: post
title: "Optimal String Modification in Java"
---

<div class="message">
It probably rarely occurs to people there could be a small bump on performance when manipulating strings in Java, since the computing power nowadays also considered to be infinite, and nobody will really try to add a dozen dead sea scrolls into one string or tweak it.
</div>

#Why should I bother!?

When it comes to critical systems, or simply for optimisation's sake, devs should be more conscious about what object to use when altering a string.

As a starter, [String](http://docs.oracle.com/javase/6/docs/api/java/lang/String.html) is the most straight forward object to use in Java. The good thing about string object is that it is an [immutable object](http://java.dzone.com/articles/why-string-immutable-java), which means it will NOT change after it is instantiated, and any modification will cause a copy of the object be created. There is a lot of benefits to this implementation like [those](http://stackoverflow.com/questions/3769607/why-do-we-need-immutable-class). But when it comes down to mass modifications, it is more of a pain in the jacksie.

#What happens now, panic?

Of course not, the clever people before us have not only come across the problem but also resolved it. And that's why, I'm going to show case the solutions so everyone can benefit from the great work.

The objects to replace simple string are [StringBuilder](http://docs.oracle.com/javase/7/docs/api/java/lang/StringBuilder.html) and [StringBuffer](http://docs.oracle.com/javase/7/docs/api/java/lang/StringBuffer.html).

Since both objects implemented the same interfaces, the methods available are almost identical. Of course I didn't compare the methods line by line, but you can take my word for it that there are only every subtle differences between them.

#So what's the difference?

In one sentence, StringBuffer is [synchronised](http://stackoverflow.com/questions/6293968/stringbuffer-is-synchronized-or-thread-safe-and-stringbuilder-is-not-why-do) while StringBuilder is not.

That makes all the difference, StringBuffer is considered safer to use in multi-thread environment while it creates unnecessary overhead for unsynchronised scenarios. But bear in mind, the masterminds in Sun (previously) perceive the world differently, StringBuffer actually came before StringBuilder. [True story](http://java67.blogspot.co.uk/2014/05/difference-between-stringbuilder-and-StringBuffer-java.html).

#Yack yack yack, prove it...

{% highlight java %}
public class Main {
    public static void main(String[] args) {
        int N = 100000;
        long t;

        {
            String s = "";
            t = System.currentTimeMillis();
            for (int i = N; i --> 0 ;) {
               s += i;
            }
            System.out.println(System.currentTimeMillis() -t);
        }

        {
            StringBuffer sb = new StringBuffer();
            t = System.currentTimeMillis();
            for (int i = N; i --> 0 ;) {
                sb.append(i);
            }
            System.out.println(System.currentTimeMillis() - t);
        }

        {
            StringBuilder sb = new StringBuilder();
            t = System.currentTimeMillis();
            for (int i = N; i --> 0 ;) {
                sb.append(i);
            }
            System.out.println(System.currentTimeMillis() - t);
        }
    }
}
{% endhighlight %}

#Conclusion

It is obvious, right? Use the StringBuilder (Force), Luke!
