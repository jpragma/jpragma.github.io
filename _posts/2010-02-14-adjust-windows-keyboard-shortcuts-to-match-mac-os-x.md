---
layout: post
title: Adjust Windows keyboard shortcuts to match Mac OS X
date: 2010-02-14 19:19
author: isaaclevin
comments: true
categories: [General]
---
I use MacBook as my primary computer at home and Windows in the office.

First half of every day I keep pressing Alt-C, Alt-V in order to copy/paste... Well, that's where Mac has its "Command" key. By the end of the day I get used to Ctrl-... combination, then I come home, turn on my MacBook and the hassle continues.

Here is solution I found:
<ol>
	<li>Download <a href="http://www.autohotkey.com/">AutoHotKey</a> and install on Windows</li>
	<li>Put following script into "Start-&gt;All Programs-&gt;Startup"(I called it <strong>mackeys.ahk</strong>):</li>
</ol>
<span style="color:#0000ff;">!c::Send ^c
!x::Send ^x
!v::Send ^v
!s::Send ^s
!a::Send ^a
!z::Send ^z
!q::Send !{F4}</span>

From now on Alt-C sends Ctrl-C, Alt-V -&gt; Ctrl-V, etc. Default Ctrl key combinations still can be used.
