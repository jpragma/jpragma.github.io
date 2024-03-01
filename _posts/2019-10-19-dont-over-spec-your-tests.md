---
layout: post
title: Don't over spec your tests
date: 2019-10-19 11:10
author: isaaclevin
comments: true
categories: [Java, Testing]
---
Yesterday, I had a discussion with my colleagues about the properties of good tests. I think in general, tests have four purposes 
in the following increasing order of importance:

1. **Validate** the correctness of the system under test.
2. **Document** usage and expectations for the tested module.
3. Help **designing** components' APIs and interactions (when practicing TDD).
4. Provide a **safety net** that enables fearless refactoring.

The last point is, in my opinion, the most important. To provide such a safety net, tests must be, as stated by Kent Beck, 
_“sensitive to changes in system behavior but insensitive to changes in code structure”_.

How do we achieve this?

Perhaps we should prefer higher-level component or module tests. Such tests are quite stable and insensitive to structural changes. 
We should limit the use of mocks in such tests, probably only mocking collaborators that live outside of the component boundaries.
We should only verify interactions with collaborators that are essential to the business logic of our component.

What do I mean by that? Often, I see unit tests where developers stub the responses of component collaborators and **then verify ALL** 
these interactions. With Mockito, they sometimes utilize “lenient” matchers like _any()_ or _isA()_ while stubbing 
and “strict” matches like _eq()_ while verifying. This technique is OK, but in my opinion, it should only be applied to true 
mocks - calls that are essential to the behavior of the system.
Calls to simple data providers (stubs) shouldn’t be verified at all; otherwise, it communicates the wrong intentions of the code author 
as well as makes tests quite fragile.

[In this article](https://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs), Martin Fowler explains well the difference between stubs and mocks. 

What do you think? How do you make your tests insensitive to structural changes?
