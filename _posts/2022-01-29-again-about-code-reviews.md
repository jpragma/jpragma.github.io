---
layout: post
title: Thoughts on code reviews (again)
date: 2022-01-29 14:18
author: isaaclevin
comments: true
categories: [General, Productivity]
---
It is hard to overestimate the role _**good**_ code reviews can play in ensuring high quality of any software. 
Unfortunately, I feel that in many teams reviews mostly become a part of a bureaucratic process, consuming lots of time and resources, but providing very little real
added value.

### Format
In the past we used to have deep in-person discussions and walk-through sessions.
Sometimes they were time-consuming,
but they almost always helped both the author and the reviewer to improve the quality and share the knowledge.
However, in recent years, it looks like there is a tendency to replace in-person reviews with a link to a diff
(because that what the PR essentially is).
No one really has time or desire to check out the branch in IDE,
categorize and organize all the files affected by the PR,
trace them in the codebase, understand how they play together and provide valuable and constructive feedback.
What do we usually see instead?
Very formalistic reviews where mostly surface-level issues are pointed out.
And if serious problems are found, then it is often too late (or at least too expensive) to fix them.

### Timing
Any feature of significant complexity requires design review and discussions.
Seeing only the final result is not enough. It is hard to understand the intentions of the author,
why certain decisions have been made, what alternates have been considered, etc.


### How we can improve the process
1. Communicate — discuss you design proposals early and often. Even with global teams working remotely, it is possible to use video conferences, screen sharing, etc. After all this is the very first item in [Agile Manifesto](https://agilemanifesto.org/). 
2. Break down your work into smaller cohesive tasks. Work on and review each task individually.
3. Start with the overall blueprint and then drill down into details. 
4. Stay on a single level of abstraction while working on and reviewing every task.