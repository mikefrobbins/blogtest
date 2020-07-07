---
ID: 16315
post_title: >
  Help in PowerShell Core is independent
  of help in Windows PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/01/27/help-in-powershell-core-is-independent-of-help-in-windows-powershell/
published: true
post_date: 2018-01-27 10:26:43
---
You've decided to install <a href="https://github.com/PowerShell/Powershell" target="_blank" rel="noopener">PowerShell Core</a> on your Windows system. First of all, keep in mind that PowerShell Core version 6.0 is not an upgrade or replacement to Windows PowerShell version 5.1. It installs side by side on Windows systems. Being aware of this makes what is shown in this blog article make more sense, otherwise it can be confusing. Based on <a href="https://twitter.com/concentrateddon/status/953749696417169408" target="_blank" rel="noopener">the response to a tweet of mine from Don Jones</a>, it appears that I’m not the only one who thought PowerShell Core should have been version 1.0 instead of 6.0 to help differentiate it and eliminate some of the confusion.

<em>Update-Help</em> has been run in Windows PowerShell to make sure the help system is 100% up to date.
<pre class="lang:ps decode:true">Update-Help</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help1a.jpg"><img class="alignnone size-full wp-image-16316" src="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help1a.jpg" alt="" width="859" height="132" /></a>

Trying to retrieve the help information for a command in PowerShell Core returns a message stating only partial help is displayed because the help files don't exist and need to be downloaded with <em>Update-Help</em>.
<pre class="lang:ps decode:true">help Get-Service -Examples</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help2a.jpg"><img class="alignnone size-full wp-image-16317" src="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help2a.jpg" alt="" width="859" height="297" /></a>

This is because the help files for PowerShell Core are located in a different location than the ones for Windows PowerShell. This means <em>Update-Help</em> has to be run in PowerShell Core to update its help files independently of Windows PowerShell. As I previously mentioned, PowerShell Core is indeed a totally separate side by side installation from Windows PowerShell.

Other differences also exist. In Windows PowerShell, the first time you ask for help on a command, it prompts you to download the help files. This occurs only if you use <em>Get-Help</em> and not the <em>Help</em> function in Windows PowerShell, but it never prompts to download the help files in PowerShell Core or at least it didn't for me.
<pre class="lang:ps decode:true ">Get-Help -Name Get-Service</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help3a.jpg"><img class="alignnone size-full wp-image-16318" src="http://mikefrobbins.com/wp-content/uploads/2018/01/pscore-help3a.jpg" alt="" width="859" height="437" /></a>

If you've running Windows PowerShell and PowerShell Core on your Windows system, don't forget to update help in both of them.

µ