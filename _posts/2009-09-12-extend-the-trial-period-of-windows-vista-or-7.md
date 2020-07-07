---
ID: 59
post_title: >
  Extend the Activation Grace Period of a
  Windows Operating System
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/12/extend-the-trial-period-of-windows-vista-or-7/
published: true
post_date: 2009-09-12 14:55:58
---
Problem:
You are evaluating or testing a Windows Operating System and need to extend the activation grace period to complete your evaluation or testing.

Solution:
Extend or rearm the evaluation period. This process must be followed before the activation countdown reaches 0:

<img class="alignnone size-full wp-image-60" title="act_8_days" src="http://mikefrobbins.com/wp-content/uploads/2009/09/act_8_days.jpg" alt="act_8_days" width="285" height="60" />

Right click “Command Prompt” and select “Run as administrator”:

<img class="alignnone size-full wp-image-62" title="cmd_admin" src="http://mikefrobbins.com/wp-content/uploads/2009/09/cmd_admin.jpg" alt="cmd_admin" width="408" height="142" />

Navigate to the C:\Windows\System32 folder and execute the following command.
<pre class="lang:batch decode:true">cscript slmgr.vbs /rearm</pre>
<img class="alignnone size-full wp-image-71" title="slmgr" src="http://mikefrobbins.com/wp-content/uploads/2009/09/slmgr2.jpg" alt="slmgr" width="460" height="119" />

Most documentation on this procedure references -rearm which works on Windows Vista and 7 but sometimes generates an error on certain Windows 2008 installations.

Restart your computer and verify the process worked:

<img class="alignnone size-full wp-image-61" title="act_30_days" src="http://mikefrobbins.com/wp-content/uploads/2009/09/act_30_days.jpg" alt="act_30_days" width="301" height="58" />

Use the following command to verify the amount of time remaining before your evaluation period expires:
<pre class="lang:batch decode:true">cscript slmgr.vbs /dli</pre>
<img class="alignnone size-full wp-image-82" title="grace" src="http://mikefrobbins.com/wp-content/uploads/2009/09/grace1.jpg" alt="grace" width="491" height="152" />

For more information:
<a href="http://support.microsoft.com/kb/948472" target="_blank" rel="noopener">http://support.microsoft.com/kb/948472</a>

µ