---
layout: post
title: "Compare Java Boolean"
tags: ["java", "boolean", "compare", "NullPointerException"]
---

<div class="message">
Once in a blue moon, some funky problem will happen in Java world.
</div>

Take a look at the following code.

{% highlight java %}
private Boolean hasBanana(final Thing thing) {
    if (thing.hasBanana() == true) {
        return true;
    } else {
        return false;
    }
}
{% endhighlight %}

It all seemed good at a glance, but what actually happened was: it throws a `NullPointerException` on invocation. But how?

# boolean vs Boolean

In Java, there is a primitive type `boolean`, which always has a value of `true` or `false`.

And there is the object type `Boolean`, it's basically a wrapper for the primitive type. The default value for object `Boolean` is either `TRUE` or `FALSE`, which is just value `true` or `false` wrapped inside `Boolean` type. As an object, it offers more than just a wrapper, such as a bunch of default methods, serialisation and etc. But that is exactly where everything all went pear shaped: the object `Boolean` can also be `null` in Java world, when the program gets `null` instead of an expected boolean value and the scenario isn't handled, `NullPointerException` was thrown.

What can be done to deal with this complication?

# Boolean Comparison

This solution should be no stranger to any Java dev, which is to perform a null check while using the Boolean value.

{% highlight java %}
private Boolean hasBanana(final Thing thing) {
    Boolean hasBanana = thing.hasBanana();
    if (hasBanana != null) {
        if (hasBanana) {
            return true;
        }
        else {
            return false;
        }
    } else {
        return null;
    }
}
{% endhighlight %}

Using the native Boolean object would look better.

{% highlight java %}
private Boolean hasBanana(final Thing thing) {
    Boolean hasBanana = thing.hasBanana();

    if (Boolean.TRUE.equals(hasBanana)) {
        return true;
    } else if (Boolean.FALSE.equals(hasBanana)) {
        return false;
    } else {
        return null;
    }
}
{% endhighlight %}

Of course, just like everything in Java, there's a corperate version.

{% highlight java %}
import org.apache.commons.lang3.BooleanUtils;

private Boolean hasBanana(final Thing thing) {
    Boolean hasBanana = thing.hasBanana();

    if (BooleanUtils.isTrue(hasBanana)) {
        return true;
    } else if (BooleanUtils.isFalse(hasBanana)) {
        return false;
    } else {
        return null;
    }
}
{% endhighlight %}

As a stretch, there's a ternary operator version of this solution if you really don't care about `null` being a legitimate value (or maybe it isn't, or at least it really shouldn't be). Then take a look at the following.

{% highlight java %}
private Boolean hasBanana(final Thing thing) {
    Boolean hasBanana = thing.hasBanana();
    return hasBanana != null ? hasBanana : false;
}
{% endhighlight %}

But that still looks terrible, don't you think?

# Optional

You may see where I am alluding to: rather than allowing program to pass `null` around and smear all over the codebase, consider `Optional` to capture the `null` value, make it visible and deal with this hidden pitfall at an appropriate stage of your business logic.

{% highlight java %}
private Optional<Boolean> hasBanana(final Thing thing) {
    Boolean hasBanana = thing.hasBanana();

    return Optional.ofNullable(hasBanana);
}
{% endhighlight %}

Smells nice!