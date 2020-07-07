---
ID: 12377
post_title: >
  Working around UAC (User Access Control)
  without running PowerShell elevated
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/09/17/working-around-uac-user-access-control-without-running-powershell-elevated/
published: true
post_date: 2015-09-17 07:30:46
---
We've all heard it and experienced the problem where you can't perform tasks such as stopping a service on the local machine with PowerShell unless your running PowerShell elevated. If the title bar doesn't say "Administrator" then you'll end up with these types of error messages:
<pre class="lang:ps decode:true">Stop-Service -Name BITS -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/uac1b.jpg"><img class="alignnone size-full wp-image-12379" src="http://mikefrobbins.com/wp-content/uploads/2015/09/uac1b.jpg" alt="uac1b" width="877" height="168" /></a>

If PowerShell remoting is enabled on the local machine, you can simply remote back into it to perform the task without having to run PowerShell elevated:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName $env:COMPUTERNAME {Stop-Service -Name BITS -PassThru} -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/uac2a.jpg"><img class="alignnone size-full wp-image-12380" src="http://mikefrobbins.com/wp-content/uploads/2015/09/uac2a.jpg" alt="uac2a" width="877" height="180" /></a>

This seems to be a great alternate instead of always requiring that PowerShell be run elevated since if you happen to do something like opening a URL in Internet Explorer from an elevated PowerShell session, your asking to <em>Get-Pwned</em> since that would mean that Internet Explorer would be running elevated as an admin and not protected by UAC.

µ