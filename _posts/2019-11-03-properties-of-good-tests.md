---
layout: post
title: Properties of good tests
date: 2019-11-03 10:54
author: isaaclevin
comments: true
categories: [Java, Testing]
---
I recently read a very interesting article called [Test Desiderata](https://medium.com/@kentbeck_7670/test-desiderata-94150638a4b3), published by Kent Beck.
In this article, Kent describes the properties of good tests. He also mentions that tests of different levels 
(unit, integration, acceptance, etc.) should focus on different properties.

Here, I'd like to discuss some of these properties and try to find some practical steps to improve our tests based on them.

> **Kent Beck says:**
> 
> _"Programmer (aka “unit” tests). Give up tests being predictive and inspiring for being writable, fast, and specific."_
> 
> _Writable — tests should be cheap to write relative to the cost of the code being tested._

So, unit tests are our first line of defense. They should be extremely cheap to write, they should be easy to read, 
and they must be very fast to execute.

Tools that help to increase the development speed of such tests are
[Spock Framework](http://spockframework.org/spock/docs/1.3/all_in_one.html) and [Groovy](https://groovy-lang.org/documentation.html) in general. 
Even if the production code is in Java, I still prefer writing my unit tests in Groovy. _"Optional typing"_, _"Multiline strings"_, 
_"Safe Navigation"_, _"Native syntax for data structures"_, and especially _"Initializing beans with named parameters"_ 
are huge productivity boosters. Check out my [presentation deck](https://goo.gl/cDpDiK) for a very basic introduction to Groovy. 
Spock takes productivity even further, allowing you to write very concise and expressive tests. 
My favorite feature is Spock's own mock/stub framework. It is way more readable than the more commonly used Mockito. 
Enabling Groovy and Spock in Java projects is simply a matter of adding an appropriate Gradle or Maven dependency.

Here is a somewhat controversial thought... If our tests are very cheap to write, maybe we should treat them as immutable. 
I recently saw a tweet suggesting that we should be allowed to write new unit tests or delete existing ones and never modify them. 
This might sound a bit extreme, but there is a rationale behind it. Quite often, our tests look good initially, 
but their quality and readability degrade with time. We refactor production code, which leads to test failures. 
We realize that the test is outdated, and we start quickly "patching" it.

> **Kent Beck says:**
>
> _Inspiring - passing the tests should inspire confidence._
>
> _Structure-insensitive — tests should not change their result if the structure of the code changes._
>
> _Predictive — if the tests all pass, then the code under test should be suitable for production._

These properties are very important for integration/end-to-end tests. I discussed this in [my previous post](../dont-over-spec-your-tests). 
When writing "stable" e2e tests, I use **real** components/collaborators for the entire system and mock only "edge" dependencies 
such as DAO repositories, JMS abstraction interfaces, REST clients, etc. I prefer creating dedicated mock implementations 
of such "edge" components rather than using raw Mockito, JMock, or Spock. 
This helps to make such tests readable for non-programmers. A typical e2e test bootstraps a specially configured application context,
invokes an entry point component, and validates expected behavior at all "edges". The value of such tests is enormous; 
they give us confidence that the entire system is working properly. 
Being structure-insensitive, e2e tests serve as a safety net when doing code refactoring.

In one of the future posts, I'll cover my experience working with legacy code and how to efficiently create e2e tests there.