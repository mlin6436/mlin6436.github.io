---
layout: post
title: "A Short Guide to AWK"
tags: ["awk", "row", "column"]
---

<div class="message">
AWK is built to process column-oriented text data, such as tables. In which a file is considered to consist of N records (rows) by M fields (columns)
</div>

#Basic

{% highlight bash %}
$ awk < file ‘{print $2}’
$ awk ‘{print $2}’ file
{% endhighlight %}

print $2 => 2nd field of the line, awk has whitespace as the default delimiter

#Delimiter

{% highlight bash %}
$ awk -F: ‘{print $2}’ file
{% endhighlight %}

-F: = use ‘:’ as delimiter

{% highlight bash %}
$ awk -F ‘[:;]’ ‘{print $2}’ file
{% endhighlight %}

-F ‘[:;]’ = use multiple delimiter, and parse using EITHER ‘:’ OR ‘;’

{% highlight bash %}
$ awk -F ‘:;’ ‘{print $2}’ file
{% endhighlight %}

 -F ‘:;’ = use multiple delimiter, and parse using ':;’ as THE delimiter

{% highlight bash %}
$ awk -F ‘:’+ ‘{print $2}’ file
{% endhighlight %}

 -F ‘:’+ = use multiple delimiter, to match any number ':'

#Arithmetic

{% highlight bash %}
$ echo 5 4 | awk '{print $1 + $2}'
{% endhighlight %}

Output is '9', the result of ‘+’ (works as addition)

{% highlight bash %}
$ echo 5 4 | awk '{print $1 $2}'
{% endhighlight %}

Output is '54', to get string concatenation

{% highlight bash %}
$ echo 5 4 | awk '{print $1, $2}'
{% endhighlight %}

Output is '5 4', to get value of 1st and 2nd field

#Variables

{% highlight bash %}
$ awk -F ‘<FS>’ ‘{print $2}’ file
{% endhighlight %}

FS, aka field separator variable, can consist of any single character or regular expression

e.g. awk -F ‘:’ ‘{print $3}’ /etc/passwd

{% highlight bash %}
$ awk -F ‘<FS>’ ‘BEGIN{OFS=“<OFS>”} {print $3, $4}’ file
{% endhighlight %}

OFS, aka output field separator variable, is the value inserted input separated output

e.g. awk -F ‘:’ ‘BEGIN{OFS=“|||”} {print $3, $4}’ /etc/passwd

{% highlight bash %}
$ awk ‘BEGIN {RS=‘<RS>’} {print $1}’ file
{% endhighlight %}

RS, aka row separator variable, it works the same as FS, but vertically

{% highlight bash %}
$ awk ‘BEGIN{ORS=“<OFS>”} {print $3, $4}’ file
{% endhighlight %}

ORS, aka output row separator variable, it works the similar way as OFS

{% highlight bash %}
$ awk ‘{print NR}’ file
{% endhighlight %}

NR, aka number of records, is equivalent to line number

It is quite helpful when calculating average

{% highlight bash %}
$ awk ‘{print NF}’ file
{% endhighlight %}

NF, aka number of fields, uses whitespace as delimiter and returns the field number the value will change when delimiter is redefined with ‘-F’

Note: awk ‘{print $NF}’ file

This will print out the last field of the line instead of number of fields, the similar usage is $0, which prints out the line

{% highlight bash %}
$ awk ‘{print FILENAME}’ file
{% endhighlight %}

It prints out the file name

{% highlight bash %}
$ awk ‘{print FILENAME, FNR}’ file1 file2
{% endhighlight %}

FNR, aka number of records for each input file, will give number of records depends on file specified
