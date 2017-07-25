---
title: Maven Command Line Application
date:  2017-07-23
tags:
  - command-line
---

Java


## Further Material

- Java Platform: [docs.oracle.com](http://docs.oracle.com/javase/8/docs)
- Java Tutorials: [docs.oracle.com](http://docs.oracle.com/javase/tutorial)
- Maven: [maven.apache.org](http://maven.apache.org)


## Topics, Tools and Terms

Java packages are distributed in _jars_ (from _**J**ava **ar**chive_).
The central repository to download these jars from is [Maven central](TODO).

JVM

Bytecode

namespace


### Dependency Management

In order to not have to download all dependencies manually, Maven does that for us.

The central configuration file for that is the file `pom.xml` (POM stands for Project Object Model) and it defines metadata, structure and dependencies of a project.
It's content is written in XML and this is what a basic `pom.xml` looks like:

{% highlight xml %}
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>guide.vanilla-project.java</groupId>
  <artifactId>maven-command-line</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>Maven Example Application</name>
  <url>https://vanilla-project.guide/java/maven-command-line</url>
</project>
{% endhighlight %}

TODO: Add hamcrest as a dependency

Let's go through that file line by line:
The `modelVersion` tells Maven about the POM version itself.
It defines what tags can be used for example.
The more interesting settings are the ones that come after that.

Java packages are primarily identified by the `groupId` and `artifactId`.
Where `groupId` is an identifier for the author of that jar.
A convention for that is to use a top-level domain in reverse order.
This would be `com.google` for jars from Google or `org.apache.maven` for Maven itself.
The `artifactId` then identifies the application/library inside the namespace of `groupId`.

The setting for `version` defines, as the name implies, the particular version of a jar file.
In our example you there's a &ldquo;SNAPSHOT&rdquo; qualifier that declares an intermediate, or &ldquo;as-yet-unreleased&rdquo; version.
Oracle's documentation has [more information about Maven version numbers](https://docs.oracle.com/middleware/1212/core/MAVEN/maven_version.htm).

For `name` we can choose whatever description we want to name our project.
Last but not least `url` can define a URL of the project itself.


### Version Managers

Version managers allow us to quickly switch between different language versions.
There's no support to automatically install new versions, though.
With Java this is a manual process.

What a version manager provides you with then are shortcuts to quickly switch between different installations on our system.

Without a version manager we'd need to always install the currently needed version of Ruby for the project we're working on.
This isn't so much a problem at the beginning, but once we work on one or two different projects that use different versions of Ruby it's helpful to be able to just switch between versions.

We recommend using a version manager in order to be able to use different ruby versions on your system.

There are several version managers available: RVM, rbenv and chruby to name a couple of popular ones.
They all solve the same problem and choosing one mostly boils down to personal preference.

A version manager will allow you to use different Ruby versions for different projects, so that you don't always need to install a new Ruby version whenever you switch to a project that needs a different version.
This can even be automated with a file named `.ruby-version` that contains the ruby version the project expects.


### Testing Tools

The standard testing framework is called [JUnit](http://junit.org).
JUnit is primarily a runner for our tests, and it comes with it's own matchers like `assertEquals`, `assertTrue`, etc.
There are alternatives to that, which provide a more fluent way to express expectations of a test.

TODO: Hamcrest or AssertJ.

In this guide we will be using Hamcrest as the matcher library.


## Directory Structure

The directory structure for a maven project consists of a `src` directory that has two sub-directories:
1. `main` for the production code.
2. `test` for the tests.

Inside each of those it's possible to have a `resources` directory to contain general assets for the project like images or configuration files.

We provided a working example of a minimal project on [Github](https://github.com/vanilla-project/java-maven-command-line).

<ul class="directory-structure">
  <li class="directory">
    src
    <ul>
      <li class="directory">main<ul><li class="directory">java</li></ul></li>
      <li class="directory">test<ul><li class="directory">java</li></ul></li>
    </ul>
  </li>
  <li class="ruby file">pom.xml</li>
</ul>


### Naming Conventions

File names of classes reflect the class name including the capital first letter.
For example the class `Vanilla` needs to be contained in file `Vanilla.java`.

Directory/package names are in lower case.

TODO: explain what a package is

Tests match their production code file names with a `Test` suffix, e.g. tests for code in `Vanilla.java` should be written in `VanillaTest.java`.

TODO: explain the concept of flattedned directories, and having the tests live in the same package


## Example Project

The repository for the example applications is available at [github.com/vanilla-project/java-maven-command-line](https://github.com/vanilla-project/java-maven-command-line).

The main application consists of basically three files:

- `Main.java` contains the main application that uses:
  - `Example.java` which contains only one method that returns a string.


### Running the Application

To run the application we need to build it first.

This can be done by executing the `package` target of maven:

{% highlight shell %}
mvn package
{% endhighlight %}

This will download all dependencies, compile the code, run the tests and _package_ it up into a `.jar` file.

We can then execute the jar file to run our application:

{% highlight shell %}
java -jar target/example-thing.jar
{% endhighlight %}


### Running the Tests

To run the tests we execute `mvn test` which then looks for all files inside directory `src/test` and runs them.
The output should look like the following:

```
$: bundle exec rspec
..

Finished in 0.00469 seconds (files took 0.07548 seconds to load)
2 examples, 0 failures
```

#### Testing Approach

The test for class `Example` is only verifying the return value of one method.

`Main` on the other hand is tested via a test-double that gets injected.
This allows us to _spy_ on the output of it.
We want to avoid printing anything to the screen while running the tests.
Injecting a test double in this instance is a nice way to isolate our application from the command line.

In the actual `main` method we then inject `$stdout`, which is Java's variable for its standard output.

