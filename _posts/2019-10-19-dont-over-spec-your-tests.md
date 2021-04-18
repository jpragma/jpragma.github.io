---
layout: post
title: Don't over spec your tests
date: 2019-10-19 11:10
author: isaaclevin
comments: true
categories: [Java, Testing]
---
<div>Yesterday I had a discussion with my colleagues about the properties of good tests. I think in general tests have 4 purposes in the following increasing order of importance:</div>
<ol>
	<li>
<div><strong>Validate</strong> correctness of the system under test</div></li>
	<li>
<div><strong>Document</strong> usage and expectations of the tested module</div></li>
	<li>
<div>Help <strong>designing</strong> component’s API and interactions (when practicing TDD)</div></li>
	<li>
<div>Provide a <strong>safety net</strong> that enables fearless refactoring</div></li>
</ol>
<div></div>
<div>The last point is the most important in my opinion. To provide such safety net, tests must be, as stated by Kent Beck, <em>“sensitive to changes in system behavior, but insensitive to changes in code structure”</em>.</div>
<div></div>
<div>How to achieve this?</div>
<div></div>
<div>Perhaps we should prefer higher-level component/module tests. Such tests are quite more stable and insensitive to structural changes. We should limit the usage of mocks in such tests, probably only mocking collaborators that live outside of the component boundaries.</div>
<div></div>
<div>We should only verify interactions with collaborators that are essential to the business logic of our component.</div>
<div></div>
<div>What do I mean by that? Often I see unit tests where developers stub responses of component collaborators and <strong>then verify ALL</strong> these interactions. With Mockito they sometimes utilize “lenient” matchers like <em>any()</em> or <em>isA()</em> while stubbing; and “strict” matches like <em>eq()</em> while verifying. This technique is OK, but in my opinion, it should only be applied to true mocks - calls that are essential to the behavior of the system.</div>
<div>Calls to simple data providers (stubs) shouldn’t be verified at all, otherwise, it delivers wrong intentions of the code author as well as makes tests quite fragile.</div>
<div></div>
<div>The difference between stubs and mocks is greatly explained <a href="https://martinfowler.com/articles/mocksArentStubs.html#TheDifferenceBetweenMocksAndStubs" target="_blank" rel="noopener">in this article</a> by Martin Fowler.</div>
<div></div>
<div>What do you think? How do you make your tests insensitive to structural changes?</div>
