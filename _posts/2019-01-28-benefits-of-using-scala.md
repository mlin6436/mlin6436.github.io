---
layout: post
title: "Benefits of Using Scala"
tags: ["scala", "benefit", "type inference", "null safety", "pattern match", "case class", "string template", "extension method"]
---

<div class="message">
It's not at all surprising that Java language feature is really falling behind for various reasons, and if you want to know what a modern language that provides functional paradigm while taking full advantage of JVM could look like, I would really recommend Scala, at least to widen your horizon. I will dive right into some of the great things that come with Scala.
</div>


# Type Inference

In Java, you need to declare the type when declaring a variable or writing a method. Whereas in Scala, you can leave this to Scala compiler to infer the type of an expression without explicit declaration.

Such as that you can declare an immutable variable easily with `val` keyword, or mutable variable with `var`.

{% highlight scala %}
val thing = "a string of thing"
{% endhighlight %}

You can also use that in method signature:

{% highlight scala %}
def doThings(x: Int) = x * x
{% endhighlight %}

Even though it's possible, my preference is always to specify return type of a method to avoid confusion when calling the method, as Scala is still a typed language at the end of day.

{% highlight scala %}
def doThings(x: Int): Int = x * x
{% endhighlight %}

# Null-safety

The most common problem everyone would have seen in Java is `NullPointerException`. Some would even argue it is a [billion-dollar-mistake](https://en.wikipedia.org/wiki/Tony_Hoare).

{% highlight scala %}
var name = null
println(name) // throws NullPointerException
{% endhighlight %}

Even though Scala inherits the sin, it's only for compatibility reasons. Scala's solution to this problem is to make the type system be explicit the use of references, using `Option`, which indicates a possibility of absence using pattern match.

{% highlight scala %}
def getName(name: Option[String]): String =
    name match {
      case Some(_) => _
      case None => "no name"
    }
println(getName(name))
{% endhighlight %}

It may seem more verbose at a glance, but using this `monadic approach` makes code more consist, and of course the method can be shorten to the following version.

{% highlight scala %}
val name: Option[String] = None
println(name.getOrElse("no name"))
{% endhighlight %}

If you want to dig deeper into different ways to express nullability in Scala, here's a list just to confuse you more.

- `Null` is a trait.
- `null` is an instance of `Null`, like in Java.
- `Nil` represents an empty list of things. It's not the same as null, but simply because the length of list is zero.
- `Nothing` is a trait, a subtype of everything, for a high level type hierarchy, follow this [link](https://docs.scala-lang.org/tour/unified-types.html).
- `None` is the Scala way to handle nullable types using a monadic approach (Option monad), which avoids the null pointer exception problem in Java by forcing the program to deal with it.
- `Unit` represents the type of method that doesn't have any return value, typically suggest the method produces side effects.

# Pattern Match

In the last section, I've touched on using pattern match to interrogate a Option monad. It appears to be similar to a `switch` statement, but there's more than meets the eye, exactly like the example. Pattern match can actually match on `type` (which are subtypes or `case classes` that inherited the same class/trait).

You might have also noticed that pattern match is able to unwrap a monad (Option monad in the last example) to provide access to the content or `guards`.

{% highlight scala %}
def getName(name: Option[String]): String =
    name match {
      case Some("default") => "can't use default as a name" // pattern guards
      case Some(str) => s"Hello: $str" // unwrap a monad
      case None => "no name"
    }
println(getName(name))
{% endhighlight %}

# Case Class

If you still weren't convinced, this is would be the one killer feature that would make you think otherwise immediately. Every Java user would know how much boilerplate code you have to write to implement a simple data model class.

{% highlight java %}
public class Employee {
    private String id;
    private String name;

    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
{% endhighlight %}

Whereas all that is a simple one-liner in Scala, along with the getters and setters.

{% highlight scala %}
case class Employee(id: String, name: String)

val employee = Employee("123", "Smith") // instantiate an Employee class
println(employee.name) // access employee field "name"
{% endhighlight %}

OK, some eagle eye might have spotted there's no `new` keyword used to instantiate a class. That is because case classes have a default `apply` method which take care of object construction. The extended version of the case class definition would look like the following, but the `val` are actually redundant.

{% highlight scala %}
case class Employee(val id: String, val name: String)
{% endhighlight %}

Of course, it's important if any field needs to be declared without public getter.

{% highlight scala %}
case class Employee(private val id: String, name: String)
{% endhighlight %}

What is even more interesting is what happens during compilation. A case class is compiled into a class and a companion object. The class itself is the same as what you would do in declaring a Java class, while `companion object` is another concept that Scala introduced. A companion object is class defined with keyboard `object`, using the same name as the accompanying class (hence the name companion object), which provides a staic execution context in relation to the related class, and it is defined in the same file as the accompanying class. Therefore, a case class can be expanded to the following code.

{% highlight scala %}
class Employee(id: String, name: String)

object Employee {
  def apply(id: String, name: String): Employee = new Employee(id, name)
}

val employee = Employee("123", "Smith")
{% endhighlight %}

In addition, case classes are compared by structure not by reference, unlike Java.

{% highlight scala %}
case class Employee(id: String, name: String)

val employee1 = Employee("123", "Smith")
val employee2 = Employee("123", "Smith")

employee1 == employee2 // output: true
{% endhighlight %}

It's also good to know that: because everything is immutable by default in Scala, you can only achieve mutability via `copy` method with case classes, which does what it says on the tin: to provide you a copy of the existing object with mutations specified.

# String Templates

Just to complete the puzzle from the previous example, string interpolation is really easy in Scala as well, just about every way possible. Here are some examples:

{% highlight scala %}
println(s"Hello: $str")
println(s"Your age is: ${age + 1}")
println(s"$employee.name has ID: $employee.id")
{% endhighlight %}

There are literally tons more I've not used before, and it's up to you to have fun.

# Extension Methods

This is a fantastic feature I personally really like, what it does is allowing users to put additional methods/behaviours to an existing class. This is particularly useful when changing/adding functionalities to a library class or some despicable old code.

{% highlight scala %}
case class Employee(id: String, name: String) {
  def getName: String = name
}

implicit class EmployeeOps(val employee: Employee) extends AnyVal {
  def getNewName: String = "hello: " + employee.name
}

val employee = Employee("123", "Smith")

println(employee.getNewName) // "hello: Smith"
{% endhighlight %}

Of course, with great power comes great responsibility, you should always use this feature with extra care, because you could easily add behaviour of a particular `class` in the whole system, such as String.

# And a lot More

On top of those features, it's hardly surprising there are more Scala can give, and you can actually start mixing Scala files into your Java project to try it out, if you don't want to fully commit to it yet. Email/Tweet me if you have something you'd like to know, I will try my best to answer, or at least give you my honest opinion about it.