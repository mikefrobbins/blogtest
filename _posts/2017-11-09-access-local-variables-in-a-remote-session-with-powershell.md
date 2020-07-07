---
ID: 15875
post_title: >
  Access Local Variables in a Remote
  Session with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/11/09/access-local-variables-in-a-remote-session-with-powershell/
published: true
post_date: 2017-11-09 07:30:42
---
Occasionally I'll come across a system with PowerShell version 2.0 on it that I need to run a remote command against which needs access to local variables. I can never remember how to accomplish that task so I thought I would write a blog about it as a reminder for myself and others who may find themselves in the same scenario.

I'll demonstrate each of the available options and whether or not they work on PowerShell version 5.1
<pre class="lang:ps decode:true">$PSVersionTable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote1a.jpg"><img class="alignnone size-full wp-image-15876" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote1a.jpg" alt="" width="859" height="213" /></a>

And PowerShell version 2.0
<pre class="lang:ps decode:true">$PSVersionTable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote2a.jpg"><img class="alignnone size-full wp-image-15877" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote2a.jpg" alt="" width="859" height="202" /></a>

First, I'll store the name of a process in a variable locally in both the PowerShell 5.1 and 2.0 console sessions I have open:
<pre class="lang:ps decode:true">$ProcessName = 'svchost'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote3a.jpg"><img class="alignnone size-full wp-image-15878" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote3a.jpg" alt="" width="859" height="57" /></a>

Trying to use the local variable in a remote session for either version generates an error message because the variable doesn't exist in the session on the remote computer:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 {
    Get-Process -Name $ProcessName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote4a.jpg"><img class="alignnone size-full wp-image-15879" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote4a.jpg" alt="" width="859" height="152" /></a>

The <em>Using</em> variable scope modifier which was introduced in PowerShell version 3.0 allows the local variable to be successfully used in a remote session on PowerShell 5.1:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 {
    Get-Process -Name $Using:ProcessName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote5a.jpg"><img class="alignnone size-full wp-image-15880" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote5a.jpg" alt="" width="859" height="286" /></a>

It generates an error when used with PowerShell 2.0 since that particular feature didn't exist until PowerShell version 3.0:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 {
    Get-Process -Name $Using:ProcessName
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote6a.jpg"><img class="alignnone size-full wp-image-15881" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote6a.jpg" alt="" width="859" height="177" /></a>

The <em>ArgumentList</em> parameter can be used with either PowerShell 2.0 or 5.1 to use a local variable in a remote session. If you're running a version that supports it, I recommend the <em>Using</em> scope modifier instead.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName dc01 -ArgumentList $ProcessName {
    Get-Process -Name $args[0]
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote7a.jpg"><img class="alignnone size-full wp-image-15882" src="http://mikefrobbins.com/wp-content/uploads/2017/11/var-remote7a.jpg" alt="" width="859" height="286" /></a>

PowerShell version 2.0 is deprecated, but that doesn't mean you won't have to occasionally deal with a system still running it. Even if the system can be updated, you can't just take production down in the middle of the day to update the version of PowerShell it's running.

<hr />

Related Blog Articles
<a href="http://mikefrobbins.com/2015/09/03/powershell-splatting-a-local-hash-table-in-a-remote-session/" target="_blank" rel="noopener">Splatting a Local Hash Table in a Remote Session</a>

µ