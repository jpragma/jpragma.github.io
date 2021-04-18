---
layout: post
title: Identifying technical debt
date: 2019-11-08 19:56
author: isaaclevin
comments: true
categories: [Java]
---
Today's very short post is about ways to identify technical debt and how to find components/classes that require immediate attention.
<ol>
	<li>Files that have a large number of git commits (they are changed often). These files are dependency magnets, they most likely violate "Open-close principle" and "Single responsibility principle". Create a report by running:[sourcecode lang="bash"]
# list repo files with their total number of commits
git log --name-only --pretty=format: | sort | uniq -c | sort -nr &gt; git_stats.txt
[/sourcecode]</li>
	<li><a href="https://www.sonarqube.org/">SonarQube</a> provides pretty good reports showing the complexity and test coverage of your classes</li>
</ol>
