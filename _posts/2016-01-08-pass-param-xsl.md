---
layout: post
title: "Passing Params to Template in XSL"
tags: ["xsl", "param", "template", "xml"]
---

<div class="message">
Recently, I am lucky enough to work on some <a href="http://www.w3.org/Style/XSL/WhatIsXSL.html">XSL</a> transforms for a legacy system. In turns out my programming skills have little use in this new found arena, even passing params around proves to be a pain. In case there are more lucky fellas out there, here's some recipes for you to meditate on.
</div>

# "apply-templates"

In XSL, one way to call a template, just like calling a function, is to use "apply-templates" keyword.

{% highlight xml %}
<xsl:template match="deal">
  <xsl:apply-templates select="product" mode="mode_of_another_template" />
</xsl:template>

<xsl:template match="product" mode="mode_of_another_template">
  ...
</xsl:template>
{% endhighlight %}

A variation of this is not to specify `mode`, then anything that matches 'product' node will be processed in the second template.

By specifying `mode`, this will allow you to pick and use the chosen template, just like calling a function with its signature.

It is all jolly good if that's all you want to do. But you may want to pass some parameters to the second template, in order to archive more complicated tasks, just like a normal function call in other programming languages.

This is what got me scratch my head so hard that I start to hear squeaking noise. What's more annoying is that it turns out to be simple, in XSL world.

A `with-param` node is what I needed.

{% highlight xml %}
<xsl:template match="deal">
  <xsl:apply-templates select="product" mode="mode_of_another_template">
    <xsl:with-param name="sort_order" select="'descending'" tunnel="yes"/>
  </xsl:apply-templates>
</xsl:template>

<xsl:template match="product" mode="mode_of_another_template">
  <xsl:param name="sort_order" tunnel="yes"/>
  ...
  <p>Sorting Order: <a href="{$sort_order}"><xsl:value-of select = "$sort_order" /></a></p>
</xsl:template>
{% endhighlight %}

Despite the straight forward looking syntax, there are some catches:

> - `tunnel` keyword is required to create a tunnel so that parameters can be passed through, [more here](http://www.saxonica.com/html/documentation/xsl-elements/with-param.html)

> - `select` keyword takes an XPath expression and returns the resulting value as parameter, to use for a string input, 'descending', DO use single quotes for it to work

> - use `$` to quote the parameter in the subsequent template

# "call-template"

Another way to call a template is to use `call-template` keyword as suggested [here](http://www.w3schools.com/xsl/el_with-param.asp).

{% highlight xml %}
<xsl:template match="deal">
  <xsl:call-template name="another_template">
    <xsl:with-param name="sort_order" select="'descending'"/>
  </xsl:call-template>
</xsl:template>

<xsl:template name="another_template">
  <xsl:param name="sort_order" tunnel="yes"/>
  ...
</xsl:template>
{% endhighlight %}

# What Else?

Ermm, can't we just all agree not to use XSL :)
