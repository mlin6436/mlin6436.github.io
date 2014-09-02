---
layout: post
title: "Removing Object Property VS. Array Element in JavaScript"
tags: ["javascript", "remove", "delete", "object", "property", "element", "array"]
---

<div class="message">
Removing object property and array element in JavaScirpt is quite a different world. It is one of defining features that differentiate prototypal from classical OO languages.
</div>

#Objects vs Arrays

First thing first, what are objects and arrays in JavaScirpt?

An [array](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array), declared using '[' and ']', acts like a [stack](https://en.wikipedia.org/wiki/Stack_%28abstract_data_type%29), is made up of a list of indexed elements. It comes with push, pop and other methods as expected, and works like a collection or list depends on which language you compare it to.

An [object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object), declared using '{' and '}', implemented like [associative arrays](https://en.wikipedia.org/wiki/Associative_array), or [set](https://en.wikipedia.org/wiki/Set_%28mathematics%29) in mathematical terms, has one or more key-value pairs as its properties. And because it is in the prototypal world, meaning objects are mutable, you can modify its properties on the fly as you wish.

And it is because of these conceptual differences, even though the action feels alike, the mechanisms to perform modification are indeed very different.  

#Deleting Properties from an Object

Assuming an objet like this:

{% highlight javascript %}
var myObj = { "name": "banana", "colour": "yellow", "texture": "mushy" }
{% endhighlight %}

The question of `'how can I remove properties from an object'` has been asked [over](http://stackoverflow.com/questions/346021/how-do-i-remove-objects-from-a-javascript-associative-array#346022) and [over](http://stackoverflow.com/questions/1784267/remove-element-from-javascript-associative-array-using-array-value#1784363) again, with surprisingly high ratings.

Apperantly, the best way is to use [delete](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/delete) operator.

{% highlight javascript %}
delete myObj.colour // return: true
{% endhighlight %}

This way, the property key value pair will be removed from the object, for good.

#Slicing and Splicing an Array

In the case of remove elements from an array, it is quite different.

Given the following array:

{% highlight javascript %}
var myArray = [ "apple", "banana", "orange" ]
{% endhighlight %}

If you try to employ the delete method like it is in object,

{% highlight javascript %}
delete myArray[1] // return: true
{% endhighlight %}

Your array will end up with a big black hole in the middle.

{% highlight javascript %}
["apple", undefined, "orange"]
{% endhighlight %}

Unless that's what you want, it is more sensible to use slice or splice to remove the elements properly.

In [slice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FArray%2Fslice), you are requesting a new array makes up from a portion of an array specified by the begin and end index. Meanwhile, the original array stays untouched.

{% highlight javascript %}
// Give the slice of elements between index 1 and 2 of the array
var result = myArray.slice(1, 2)
// result = ["banana"]

// Give the slice of elements from index 1 to the end of the array
result = myArray.slice(1)
// result = ["banana", "orange"]

// Give the whole array
result = myArray.slice()
// result = ["apple", "banana", "orange"]
{% endhighlight %}

In [splice](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/splice?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FGlobal_Objects%2FArray%2Fsplice), `'slice and place'` as I interprete, will change the content of the array, with the specified indexes and the added content.

{% highlight javascript %}
// remove 0 elements from index 1, and insert "peach"
var removed = myArray.splice(1, 0, "peach")
// removed = [], myArray = ["apple", "peach", "banana", "orange"]

// remove 1 element form index 3
removed = myArray.splice(3, 1)
// removed = ["orange"], myArray = ["apple", "peach", "banana"]

// remove 2 elements form index 0, and insert "grape", "kiwi"
removed = myArray.splice(0, 2, "grape", "kiwi")
// removed = ["apple", "peach"], myArray = ["grape", "kiwi", "banana"]

// remove all elements from index 2 to the end
removed = myArray.splice(2)
// removed = ["banana"], myArray = ["grape", "kiwi"]
{% endhighlight %}

Just to sum up, you can also apply this fantastic [way](http://stackoverflow.com/questions/281264/remove-empty-elements-from-an-array-in-javascript#281335) to clean up all the empty elements in an array.

{% highlight javascript %}
Array.prototype.clean = function(deleteValue) {
  for (var i = 0; i < this.length; i++) {
    if (this[i] == deleteValue) {
      this.splice(i, 1);
      i--;
    }
  }
  return this;
};

test = new Array("","One","Two","", "Three","","Four").clean("");

test2 = [1,2,,3,,3,,,,,,4,,4,,5,,6,,,,];
test2.clean(undefined)
{% endhighlight %}
