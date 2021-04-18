---
layout: post
title: Finding unused code in Java projects
date: 2010-02-14 19:24
author: isaaclevin
comments: true
categories: [Java]
---
In many projects, especially big ones, developed over several years, there is a lot unused code. Cleaning up projects and deleting such unused code is important.
<ul>
	<li>Eclipse can detect unused methods and variables.</li>
	<li><a href="http://pmd.sourceforge.net/">PMD</a> (and <a href="http://maven.apache.org/plugins/maven-pmd-plugin/">PMD maven plugin</a>) can also do this and even be integarated into <a href="http://en.wikipedia.org/wiki/Continuous_Integration">CI</a> process.</li>
	<li><a href="http://www.eclipse.org/eclipse/platform-core/downloads.php">Eclipse Core Tools</a> plugin can find unused classes (see description <a href="http://blogs.infosupport.com/blogs/peterhe/archive/2007/12/15/Finding-unused-code.aspx" target="_blank">here</a>).</li>
</ul>
Unfortunately all above methods will mark code invoked by reflection (e.g. using IoC container such as Spring) as unused. At the same time garbage code that was left behind together with its unit test won't be detected.

I see only one way to detect such obsolete code - run the system for a while through all typical scenarios and collect statistics on loaded classes, invoked methods, etc. Of course report generated using this technique can be only considered as a hint and manual verification is required.
Proper solution should probably be done using AOP, instrumenting code either during compile or load time. There is however quick and dirty way that archives similar results.
<ol>
	<li>Run your app with -verbose:class option (send standard output to file in addition to console using | tee &lt;filename&gt;)
Example:
<code>$ export MAVEN_OPTS="$MAVEN_OPTS -verbose:class"
$ mvn clean jetty:run | tee jetty.log</code></li>
	<li>Execute all typical scenarios of your application</li>
	<li>From generated log file create sorted list of your project classes used in executed scenarios:
<code>$ grep "\[Loaded.*com.jpragma" jetty.log | awk '{print $2}' | sort | uniq -u &gt; loaded_classes.txt</code></li>
	<li>Create sorted list of your compiled project classes:
<code>$ find target/classes/ -name *.class | sed 's/target\/classes\/\///g' \
| sed 's/.class//g' | tr -s '/' '.' \
| sort &gt; declared_classes.txt</code></li>
	<li>Compare two txt files finding all classes that declared but not used:
<code>$ diff loaded_classes.txt declared_classes.txt | grep "&gt;"</code></li>
</ol>
