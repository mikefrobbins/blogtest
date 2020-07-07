---
ID: 15598
post_title: >
  Detect the presence of and remove
  CCleaner with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/09/18/detect-the-presence-of-and-remove-ccleaner-with-powershell/
published: true
post_date: 2017-09-18 20:44:11
---
Based on <a href="http://www.piriform.com/news/blog/2017/9/18/security-notification-for-ccleaner-v5336162-and-ccleaner-cloud-v1073191-for-32-bit-windows-users" target="_blank" rel="noopener">the news today</a>, I thought I would share a couple of PowerShell code snippets to detect the presence of and silently uninstall CCleaner.

You can detect the presence of CCleaner along with the version of it you have installed via the registry.
<pre class="lang:ps decode:true">Get-ItemProperty -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object DisplayName -eq CCleaner</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner1b.jpg"><img class="alignnone size-full wp-image-15609" src="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner1b.jpg" alt="" width="859" height="308" /></a>

You can use a similar command to run its uninstaller silently if it's detected.
<pre class="lang:ps decode:true">if (Get-ItemProperty -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
    Where-Object DisplayName -eq CCleaner -OutVariable Results) {
    &amp; "$($Results.InstallLocation)\uninst.exe" /S
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner2b.jpg"><img class="alignnone size-full wp-image-15610" src="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner2b.jpg" alt="" width="859" height="92" /></a>

Give it a couple of minutes and then run the first command again to verify it has been removed.
<pre class="lang:ps decode:true">Get-ItemProperty -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object DisplayName -eq CCleaner</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner3b.jpg"><img class="alignnone size-full wp-image-15611" src="http://mikefrobbins.com/wp-content/uploads/2017/09/remove-ccleaner3b.jpg" alt="" width="859" height="67" /></a>

If PowerShell remoting is enabled on the computers in your environment, you can simply wrap these commands inside of <em>Invoke-Command</em> to detect and remove CCleaner from the machines in your environment. You could also consider adding the command to remove CCleaner to your login script since PowerShell remoting is not enabled by default on desktop operating systems.

The commands shown in this blog article are written to be compatible with PowerShell version 3.0 and higher. They could be made to be compatible with PowerShell version 2.0 (<a href="https://blogs.msdn.microsoft.com/powershell/2017/08/24/windows-powershell-2-0-deprecation/" target="_blank" rel="noopener">PowerShell version 2.0 is deprecated</a>).

<hr />

Update - September 20th, 2017
While it doesn't appear to be possible to install the 32bit version of CCleaner on a 64bit operating system (at least not with the current version), you may also want to check the Wow64 registry settings just in case. I even went so far as to download CCleaner on a 32bit OS and install that version on a 64bit OS which still installed the 64bit version.
<pre class="lang:ps decode:true ">Get-ItemProperty -Path HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\* |
Where-Object DisplayName -eq CCleaner</pre>
I thought about this yesterday when that registry key was referenced in <a href="https://twitter.com/Ruben8Z/status/910290335766630400" target="_blank" rel="noopener">a response to an unrelated Tweet</a> of mine. When it was mentioned again today by <a href="https://twitter.com/Binneyj79/status/910566405640261632" target="_blank" rel="noopener">Josh Binney</a>, I figured it was worth adding to this blog article.

<hr />

Based on one of the comments to this blog article, older versions of CCleaner may not be found using the PowerShell eq operator so you may want to consider switching to either the like or match operator. See the comments to this blog article for more details.

µ