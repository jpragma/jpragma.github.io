---
layout: post
title: Useful options running "mvn test"
date: 2011-01-28 12:58
author: isaaclevin
comments: true
categories: [Java]
---
There are some very useful command line options you can specify when executing maven tests:

<strong>-Dtest=[testname]</strong>, this allows selective execution of unit tests, instead of running them all. [testname] might be full class name, class name without package, or even wildcard.

<strong>-Dsurefire.useFile=false</strong>, this will force surefire plugin to write failing test info to stdout instead of file in target/surefire-reports/. Can be a huge time saver.



