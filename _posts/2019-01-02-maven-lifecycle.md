---
layout: post
title: "Intro to Maven Lifecycle"
tags: ["maven", "lifecycle"]
---

<div class="message">
This article aims to give a quick intro to Maven Lifecycle.
</div>

# Lifecycle 

Maven is basically built round the concept of lifecycle, which means the process for compiling, testing, building and distributing of a particular artifact is clearly defined. There are three built-in build lifecycles: `default`, `clean` and `site`. Each lifecycle is made up of multiple `phases`. The execution of lifecycle is sequential, which means Maven will run all the phase(s) in every project/subproject upto the target phase in order.

__default__, which is responsible for project deployment, consists of the following phases.

- __validate__
- initialize
- generate-sources
- process-sources
- generate-resources
- process-resources
- __compile__
- process-classes
- generate-test-sources
- process-test-sources
- generate-test-resources
- process-test-resources
- test-compile
- process-test-classes
- __test__
- prepare-package
- __package__
- pre-integration-test
- integration-test
- post-integration-test
- __verify__
- __install__
- __deploy__

__clean__ cleans up artifacts created by prior builds.

- pre-clean
- __clean__
- post-clean

__site__ generates site documentation for this project.

- pre-site
- __site__
- post-site
- site-deploy

The phases named with hyphenated-words (which are not highlighted in the above lists) produce intermediate results for the lifecycle, which allows various plugins, such as JaCoCo, may bind `plugin goals` to those phases to prepare execution environment. But those phases are normally not called from commnad line direclty for the reason that it may leave processes hanging and even Maven not terminating properly.

{% highlight shell %}
mvn clean deploy
{% endhighlight %}

This command execute each lifecycle phases in order: __clean__, __validate__, __compile__, __test__, __package__, __verify__, __install__ and finally __deploy__.

# Goal

Goal (plugin goal) is the smallest unit to construct a Maven build phase, which specifies a task to build and manage a project. It can either be bound to one or more build phases, or be executed directly independent of any lifecycle/phase.

{% highlight shell %}
mvn clean dependency:copy-dependencies package
{% endhighlight %}

__clean__ and __package__ are both build phases, while __dependency:copy-dependencies__ is a build goal, which is executed after clean and before package. In addition, if dependency:copy-dependencies is attached to any other phases, it will also be triggered when the specific phase(s) are executed.

# Binding

Binding is a way to attach execution goals to specific phases in Maven lifecycles. There are two major ways to achieve this via configuration: `packaing` and `plugins`.

`packaging` is the most common way to add multiple goals to multiple phases, by adding the following configuration to __pom.xml__ root.

{% highlight xml %}
<project>
  ...
  <packaging>war</packaging>
  ...
</project>
{% endhighlight %}

Some of the `<packaging>` configuration value are: `pom`, `jar`, `ejb`, `war`, `ear` and `rar`. The default value is `jar` if the configuration is absent. A complete list of standard bindings can be found [here](https://maven.apache.org/ref/3.6.0/maven-core/default-bindings.html).

On top of `packaging`, `plugin` is another way to add goals to phases. It binds a goal directly to a set of pre-defined phase(s), just like packaing. Alternatively, it can be added to a specific phase, like the following

{% highlight xml %}
<project>
  ...
  <build>
     <plugin>
      <groupId>com.mycompany.example</groupId>
      <artifactId>display-maven-plugin</artifactId>
      <version>1.0</version>
      <executions>
        <execution>
          <phase>process-test-resources</phase>
          <goals>
            <goal>time</goal>
          </goals>
        </execution>
      </executions>
    </plugin>
  </build>
  ...
</project>
{% endhighlight %}