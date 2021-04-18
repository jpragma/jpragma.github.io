---
layout: post
title: Integration testing of a legacy code
date: 2020-04-10 11:23
author: isaaclevin
comments: true
categories: [Java, Testing]
---
Wouldn't it be nice to work only on greenfield projects with the team that shares the same vision and style? Unfortunately, we have to spend a significant portion of our professional life dealing with the messy legacy code. Such code is very hard to comprehend. It often consists of tangled classes, with some arbitrary division of responsibilities. Unit test coverage is often very low. Sometimes unit tests are formally there, but you can clearly see that they have been written after the fact, simply repeating the production code mess. These tests are very fragile and don't provide adequate "safety net".

Before trying to refactor such legacy code we must first create good end-to-end tests. Tests that are, as Kent Beck says, <em>"sensitive to changes in system behavior, but insensitive to changes in code structure"</em>.

Let's assume your project is a typical SpringBoot application. We have some complex functionality, that works, but we don't fully understand how. We want to do some "forensic analysis" of its implementation and cover it with a stable integration e2e test. We know that this functionality starts with externally exposed REST API. We know that after going through layers of services, managers, helpers, utility classes, repositories, etc. it stores some data in the database and also makes some calls to external systems using REST and/or JMS.

Here is the plan:
<ol>
	<li> Assuming JUnit 5, create test class "XyzComponentIT" and annotate it with <em>@ExtendWith(SpringExtension.class)</em> and <em>@ContextConfiguration(classes=XyzConfig.class)</em>. This XyzConfig class should be annotated with <em><span style="text-align:left;color:#2c3338;text-transform:none;line-height:1.2em;text-indent:0;letter-spacing:normal;font-family:'Noto Serif', Georgia, 'Times New Roman', Times, serif;font-size:16px;font-variant:normal;font-weight:400;text-decoration:none;word-spacing:0;display:inline !important;white-space:normal;cursor:text;orphans:2;float:none;background-color:#ffffff;">@TestConfiguration</span></em> and will be used to create Spring beans using @Bean instead of relying on a component scan. This way we will know exactly which beans participate in our workflow.</li>
	<li> Create the first bean for the entry point (REST controller) and inject it into the test using @Autowired</li>
	<li> Create a test case method that invokes this entry point passing some typical payload into it.</li>
	<li> If you try to run the test at this point, it will fail to initialize the Spring context - this is expected, we didn't provide any controller's dependencies yet. Let's start creating them one by one.</li>
	<li> Since we are writing end-to-end integration tests, we want to create beans using a real implementation of every dependency, except <strong><em>"edge components"</em></strong>. Edge components are those that encapsulate communication with external systems - e.g. database repositories, HTTP and JMS clients, etc.</li>
	<li>Beans of edge components should be mocked. One very useful technique here is to use <strong><em>"strict mocks"</em></strong>. "Strict mocks" fail on every call that is not explicitly stabbed. This way we can identify the exact external system communication and crosscheck it with the business requirements. Below I included a snippet of  Mockito based implementation of such strict mocks. For better readability, consider encapsulating such mock beans in properly designed test doubles.</li>
	<li> Next, implement the actual test case method, stubbing calls that read external data and verifying essential interactions with the external systems. For example, if our workflow updates the customer profile in the database and sends notifications via email service, then we should verify all interactions with test doubles that encapsulate DAO and email server communication.</li>
</ol>
Integration tests described here are quite stable since they have minimal knowledge of the internal structure and implementation details of our components. Since they consider the whole system as a black box, they are an essential tool that gives us confidence while doing code refactoring.

Here is the [GIST](https://gist.github.com/jpragma/226893c1bde182973ab842660a36c99c) of a very simplified example:
