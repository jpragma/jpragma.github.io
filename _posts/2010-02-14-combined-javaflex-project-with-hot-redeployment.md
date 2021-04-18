---
layout: post
title: Combined Java+Flex project with hot redeployment
date: 2010-02-14 19:16
author: isaaclevin
comments: true
categories: [Flex, Java]
---
In small applications it makes sense to combine Java and Flex code under same project. I'm a big fan of maven and one of my favorite maven plugins is <a href="http://mojo.codehaus.org/jetty-maven-plugin/usage.html">embedded jetty container</a>. This plugin runs jetty directly from maven and supports automatic monitoring and redeployment of java classes and resources. I thought it would be nice to run Java+Flex projects in the same way and have updated SWF file instantly available in the browser immediately after Flex Builder compiles it.

You can find step-by-step instructions on such project set up on my wiki: <a href="http://wiki.jpragma.com/creating-new-javaflex-project">http://wiki.jpragma.com/creating-new-javaflex-project</a>
