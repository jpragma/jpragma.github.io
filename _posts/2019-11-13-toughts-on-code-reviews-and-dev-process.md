---
layout: post
title: Toughts on code reviews and dev process
date: 2019-11-13 13:40
author: isaaclevin
comments: true
categories: [General, Productivity]
---
Nowadays most software projects set the master branch to be read-only, use pull requests to merge feature branches and require code reviews with multiple approvals. Undoubtedly this a good practice in theory, however, as usual, the devil is in the detail, and quite often typical code reviews are useless and even harmful.
<ul>
	<li>Most standard code review tools display all changes in a flat list, so the context of these changes is completely lost. The order, in which files appear has nothing to do with the logical organization of the change (and the codebase in general).</li>
	<li>It is extremely hard to reason about changes larger than just a few files with more than just 10-20 added/modified lines of code in each - especially when these changes mix design concepts and low-level implementations of particular functions. It requires a very deep context switch and extreme discipline from the reviewer.</li>
	<li>As a result, many reviews focus only on very simple things. Things that could and should be discovered by code analysis tools, IDE inspections, SonarQube and other automatic quality gates. Reviewer's time is expensive, code analysis tools are cheap.</li>
	<li>When PR is submitted for the review, the code is already written. It is often too late to comment on the fundamental problems in the design. Such comments are hard to address and naturally, they are met with lots of resistance and even anger from the code author.</li>
	<li>Often there is a tight deadline to deliver the feature, so the pressure goes on the reviewer - <em>"It is his fault we didn't deliver the fix on time, he didn't timely approve my code change"</em>.</li>
	<li>Typically author of the PR selects the reviewers. This leads to uneven distribution of the reviews (which consume significant time) or sometimes it promotes selecting reviewers that "go easy on everyone".</li>
</ul>
<strong>What's the solution? </strong>

I propose using the following process:
<ol>
	<li>Developer starts working on the assigned feature/ticket, e.g. TICKET-123.</li>
	<li>Create and check out feature branch - <strong><em>feature/t123/my-feature</em></strong>.</li>
	<li>Analyze the feature and if it is complex enough write a design document. Depending on the complexity, this could be just a few sentences in the <a href="https://en.wikipedia.org/wiki/Markdown">MD</a> file or full wiki page with diagrams, etc.</li>
	<li>Create another branch <strong><em>feature/t123/my-feature-design</em></strong>, commit there your design document (or link to it) and submits the PR with overall <strong><em>feature/t123/my-feature</em></strong> as a target. Some projects might require that only tech leads or architects could review such "design" PRs.</li>
	<li>Once "Design PR" is approved, merge it and creates another branch <strong><em>feature/t123/my-feature-blueprint</em></strong>. The purpose of this branch is to create the overall skeleton of the feature, adding lots of placeholders for every implementation detail. The more placeholders you add the better it is. Each such placeholder becomes a subtask of the feature. These placeholders might use some special markers like <span style="color:#008000;"><em>// PH-1 User input validation</em></span>, and your IDE can be easily configured to display the consolidated list of such markers. After this is done, submit the PR to merge <strong><em>feature/t123/my-feature-blueprint </em></strong>into <strong><em>feature/t123/my-feature. </em></strong>Here your reviewers will have an opportunity to evaluate the overall feature design at the code level and express their concerns or suggestions.</li>
	<li>Next, implement every placeholder defined in the previous step. Each placeholder is implemented in a separate branch that is forked from <strong><em>feature/t123/my-feature</em></strong> and dedicated PR is submitted for each. Note that at this stage "placeholder" branches could be implemented in parallel by several developers. Ideally, reviewers of these placeholder implementation PRs should be assigned randomly, promoting a balanced workload as well as knowledge sharing within the development team.</li>
	<li>Once all placeholders are resolved, submit the final PR merging the overall <strong><em>feature/t123/my-feature</em></strong> into <em><strong>master</strong></em>. Since every change in this PR is already reviewed and approved, the goal of this PR is to simply verify the commit history of the <strong><em>feature/t123/my-feature</em></strong> branch and make sure no direct code commits are done.</li>
	<li>Finally, merge the feature branch into the master.</li>
</ol>
This process has many advantages. First of all, reviewers have a chance to comment on feature design before too many resources are spent on implementation. Secondly, instead of one huge PR, we are dealing with several small PRs each one staying at a single level of abstraction and never mixing the overall design with small details of a particular step. Each such PR is easy to understand and provide meaningful comments. It promotes knowledge sharing in the team and minimizes frustration and conflicts when some big PRs are rejected.

As usual comments, opinions, and criticism are more than welcome!
