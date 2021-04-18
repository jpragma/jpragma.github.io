---
layout: post
title: Using Groovy in JMeter
date: 2010-02-14 19:12
author: isaaclevin
comments: true
categories: [Java]
---
<em>Note: This is a copy from my old blog posted in 2007. Some things mentioned here have been changed since then.</em>

Groovy is a great scripting language intimately integrated with Java. Being able to leverage any code written in Java and at the same time having all the advantages of dynamic programming language, makes it an excellent choice for tasks such as file management, build systems and testing.

JMeter is an open source load test tool. It supports many protocols (HTTP, FTP, JMS and others) out-of-the-box.

Many times I found myself in situation when I wanted to run load test against stand-alone Java components or against an application that uses proprietary binary TCP based protocol. Groovy would be an excellent choice for this task. Some work on integrating JMeter and Groovy was done by&nbsp;<a href="http://themindstorms.blogspot.com/2007/01/groovy-support-for-jmeter.html" target="_blank">Alexandru Popescu</a>, however the project is not available to the public yet. Then I discovered that there is an indirect support for groovy in JMeter itself. JMeter supports BSF, which is an abstraction layer on top of many scripting languages including groovy. BSF distribution that comes with JMeter actually has some glitch in groovy support, but after upgrading to the latest version of bsf.jar it was very easy to start writing JMeter scripts in groovy.

Here the steps I followed:
<ol>
	<li>download latest groovy binaries from&nbsp;<a href="http://groovy.codehaus.org/" target="_blank">http://groovy.codehaus.org/</a></li>
	<li>copy groovy-all-1.0.jar to jmeter/lib directory</li>
	<li>download latest bsf binaries from&nbsp;<a href="http://jakarta.apache.org/bsf/" target="_blank">http://jakarta.apache.org/bsf/</a> (I was using version 2.4.0)</li>
	<li>replace bsf-2.3.0.jar in jmeter/lib with bsf.jar from the version you just downloaded</li>
	<li>start jmeter</li>
	<li>create BSF Sampler and specify "groovy" as a scripting language (see screenshot below)</li>
	<li>type your groovy script and run it</li>
</ol>
<img src="http://jpragmainc.files.wordpress.com/2010/02/jmeter-groovy-bsf1.jpg" alt="" title="jmeter-groovy-bsf" width="524" height="285" class="alignnone size-full wp-image-23" />

This method is far from being perfect. It actually monitors how much time it takes to execute the script itself, but in most cases you'd like to specify exactly when to start counting execution time [results.sampleStart(); results.samplePause(); results.sampleResume(); results.sampleEnd();]
I hope Alexandru will make his project available soon, or may be I'll find some time to create more sophisticated JMeter Groovy plug-in.

Another issue is how to transfer data between several scripts. Let's say you'd like to create 1st script that establishes TCP connection; 2nd that sends proprietary login message and gets back some security token; 3rd that sends some business message (using the same TCP connection and providing the token) and 4th that just disconnects. How to transfer Socket and Token objects between scripts? Well, you can use ThreadLocal. Following code snippet defines ThreadLocalStorage class that can be used from the scripts (isn't it great that you can just call Java from Groovy?) to save and retrieve any data:

[sourcecode language="java"]
package com.jpragma.utils;

import java.util.HashMap;

public class ThreadLocalStorage {
  private static ThreadLocal storage = new ThreadLocal() {
    protected Object initialValue() {
      return new HashMap();
    }
  };
  
  public static Object get(String name) {
    HashMap vars = (HashMap) storage.get();
    return vars.get(name);
  }
  
  public static void set(String name, Object value) {
    HashMap vars = (HashMap) storage.get();
    vars.put(name, value);
  }
}
[/sourcecode]

Just package it as a jar file and put into jmeter/lib directory. Now you can write your scripts like this:

<strong>Connect:</strong>
[sourcecode language="groovy"]
import com.jpragma.utils.ThreadLocalStorage

def socket = new Socket(&quot;localhost&quot;, 18012);
def iStream = new DataInputStream(socket.getInputStream());
def oStream = new DataOutputStream(socket.getOutputStream());

ThreadLocalStorage.set('mySocket', socket)
ThreadLocalStorage.set('myInStream', iStream)
ThreadLocalStorage.set('myOutStream', oStream)

return &quot;success&quot;
[/sourcecode]

<strong>Echo Message</strong>
[sourcecode language="groovy"]
import com.jpragma.utils.ThreadLocalStorage

def iStream = ThreadLocalStorage.get('myInStream')
def oStream = ThreadLocalStorage.get('myOutStream')

def str = &quot;My String&quot;
oStream.writeInt(str.length())
oStream.write(str.bytes)

def msgSize = iStream.readInt()
def buf = new byte[msgSize]
iStream.readFully(buf)
def responseStr = new String(buf)

if (str == responseStr)
    return &quot;success&quot;
else
    return &quot;failure&quot;
[/sourcecode]

<strong>Disconnect</strong>
[sourcecode language="groovy"]
import com.jpragma.utils.ThreadLocalStorage

def socket = ThreadLocalStorage.get('mySocket')
if (socket != null) {
    socket.close()
}
return &quot;success&quot;
[/sourcecode]
