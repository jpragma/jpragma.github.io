---
layout: post
title: Thoughts on code reviews and the development process
date: 2019-11-13 13:40
author: isaaclevin
comments: true
categories: [General, Productivity]
---
These days, most software projects set the master branch to be read-only, use pull requests to merge feature branches, and 
require code reviews with multiple approvals. Theoretically, this is a great practice, no doubt; but, as always, 
the devil is in the details, and typical code reviews are frequently ineffective or even harmful.

- Most standard code review tools display all changes in a flat list, so the context of these changes is completely lost. 
The order in which files appear has nothing to do with the logical organization of the change (or the codebase in general).
- It is extremely hard to reason about changes larger than just a few files with more than just 10-20 added/modified lines of code in each, 
especially when these changes mix design concepts and low-level implementations of particular functions. It requires a very deep context switch 
and extreme discipline from the reviewer.
- As a result, many reviews focus only on very simple things. Things that could and should be discovered by code analysis tools, 
IDE inspections, SonarQube, and other automatic quality gates. The reviewer's time is expensive, and code analysis tools are cheap.
- When PR is submitted for review, the code is already written. It is often too late to comment on the fundamental problems in the design.
Such comments are hard to address, and naturally, they are met with lots of resistance and even anger from the code author.
- Often there is a tight deadline to deliver the feature, so the pressure goes on the reviewer: 
_"It is his fault we didn't deliver the fix on time, he didn't timely approve my code change"_.
- Typically, the author of the PR selects the reviewers. This leads to an uneven distribution of the reviews (which consume 
significant time), or sometimes it promotes selecting reviewers that "go easy on everyone".

**What's the solution?**

I propose using the following process:

1. The developer starts working on the assigned feature/ticket, e.g., TICKET-123.
2. Create and check out the feature branch: **_feature/t123/my-feature_**.
3. Analyze the feature, and if it is complex enough, write a design document. Depending on the complexity, this could be 
just a few sentences in the [MD](https://en.wikipedia.org/wiki/Markdown) file or a full wiki page with diagrams, etc.
4. Create another branch _**feature/t123/my-feature-design**_, commit your design document (or link to it) there, and submit the PR 
with the overall _**feature/t123/my-feature**_ as a target. Some projects might require that only tech leads or architects review such "design" PRs.
5. Once "Design PR" is approved, merge it and create another branch _**feature/t123/my-feature-blueprint**_. 
The purpose of this branch is to create the overall skeleton of the feature, adding lots of placeholders for every implementation detail.
The more placeholders you add, the better it is. Each such placeholder becomes a subtask of the feature. 
These placeholders might use some special markers, like <span style="color:#008000;"><em>// PH-1 User input validation</em></span>, 
and your IDE can be easily configured to display the consolidated list of such markers. After this is done, submit the PR 
to merge _**feature/t123/my-feature-blueprint**_ into _**feature/t123/my-feature**_. Here, your reviewers will have an opportunity 
to evaluate the overall feature design at the code level and express their concerns or suggestions.
6. Next, implement every placeholder defined in the previous step. Each placeholder is implemented in a separate branch that 
is forked from _**feature/t123/my-feature**_, and a dedicated PR is submitted for each. Note that at this stage, "placeholder" 
branches could be implemented in parallel by several developers. Ideally, reviewers of these placeholder implementation PRs 
should be assigned randomly, promoting a balanced workload as well as knowledge sharing within the development team.
7. Once all placeholders are resolved, submit the final PR merging the overall _**feature/t123/my-feature**_ into the _**main**_ branch. 
Since every change in this PR has already been reviewed and approved, the goal of this PR is to simply verify the commit 
history of the _**feature/t123/my-feature**_ branch and make sure no direct code commits are done.
8. Finally, merge the feature branch into the master.

This process has many advantages. First of all, reviewers have a chance to comment on feature design before too many resources 
are spent on implementation. Secondly, instead of one huge PR, we are dealing with several small PRs, each one staying 
at a _**single level of abstraction**_ and never mixing the overall design with the small details of a particular step. 
It is easy to understand and provide meaningful comments for such PRs. 
It promotes knowledge sharing in the team and minimizes frustration and conflicts when some big PRs are rejected.

As usual, comments, opinions, and criticism are more than welcome!