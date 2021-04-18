---
layout: post
title: Control Log4j message timezone
date: 2010-12-27 15:23
author: isaaclevin
comments: true
categories: [Java]
---
If your server is running in one timezone, but you want to have log messages be printed using different timezone, here is a simple solution:
<ol>
	<li>Add <a href="http://logging.apache.org/log4j/companions/extras/download.html">log4j extras</a> to project classpath (maven GAV is <em>log4j:apache-log4j-extras:1.0</em>)</li>
	<li>In log4j.xml use EnhancedPatternLayout, specifying required timezone for logging purposes (see example below for EST TZ)</li>
</ol>
[sourcecode lang="xml"]
&lt;appender name=&quot;CONSOLE&quot; class=&quot;org.apache.log4j.ConsoleAppender&quot;&gt;
    &lt;param name=&quot;Threshold&quot; value=&quot;TRACE&quot; /&gt;
    &lt;layout class=&quot;org.apache.log4j.EnhancedPatternLayout&quot;&gt;
        &lt;param name=&quot;ConversionPattern&quot; value=&quot;%d{ISO8601}{EST} %-5p [%t][%c:%M(%L)] %m%n&quot; /&gt;
    &lt;/layout&gt;
&lt;/appender&gt;
[/sourcecode]

In this example, log messages will be printed using EST regardless of your server timezone.
