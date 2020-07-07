---
ID: 16604
post_title: >
  Use PowerShell to Install Windows
  Features and Reboot
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/06/21/use-powershell-to-install-windows-features-and-reboot/
published: true
post_date: 2018-06-21 07:30:51
---
Recently, I installed the Windows Subsystem for Linux (WSL) feature on my Windows 10 computer. Microsoft has <a href="https://docs.microsoft.com/en-us/windows/wsl/install-win10" target="_blank" rel="noopener">an installation guide</a> that walks you through the entire process, but I thought I'd share a few PowerShell tricks when it comes to installing Windows features.

The system used throughout this blog article runs Windows 10 version 1803 which ships with Windows PowerShell version 5.1. Your mileage may vary if you're using a different version of Windows and/or PowerShell.

Run Windows PowerShell elevated as an administrator. Add the Windows Subsystem for Linux feature.
<pre class="lang:ps decode:true ">Enable-WindowsOptionalFeature -FeatureName Microsoft-Windows-Subsystem-Linux -Online</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl1a.jpg"><img class="alignnone size-full wp-image-16605" src="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl1a.jpg" alt="" width="858" height="87" /></a>

A reboot is required and I thought this was kind of a bummer until I discovered that the <em>Enable-WindowsOptionalFeature</em> cmdlet doesn't have a parameter to make it go ahead and automatically reboot if one is needed. That's the real bummer :-(. Well, not really, because PowerShell can be used to solve that problem.

Specify the <em>NoRestart</em> parameter to make it give your prompt back. Luckily, the <em>RestartNeeded</em> property is returned as an object-oriented result and not just output to the screen. Yes, there's a difference. This allows you to store the results in a variable and perform logic on them to automatically reboot if needed. By the way, this will work with any feature added with <em>Enable-WindowsOptionalFeature</em>, not just the one shown in this blog article.
<pre class="lang:ps decode:true ">Enable-WindowsOptionalFeature -FeatureName Microsoft-Windows-Subsystem-Linux -Online -NoRestart -OutVariable results
if ($results.RestartNeeded -eq $true) {
  Restart-Computer -Force
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl2a.jpg"><img class="alignnone size-full wp-image-16606" src="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl2a.jpg" alt="" width="859" height="243" /></a>

Well, that's nice and all, but some of the command output as shown in the previous example is still displayed on the screen along with this progress information shown in the following example.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl3a.jpg"><img class="alignnone size-full wp-image-16607" src="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl3a.jpg" alt="" width="859" height="98" /></a>

How can you silence all of that and just make it so without displaying any results? Most people don't know about the <em>$ProgressPreference</em> global variable and while it's generally not considered to be a best practice to change the value of these types of global preference variables, it's what must be done in this scenario if you want to silence the progress information. I always like to say best practices are a starting point, not an ending one. Anytime you're going to change the values of these types of global preference variables, store the current value in a variable, change it, run the command, and then change it back to it's previous value immediate after the command finishes. It's also worth noting that changes to these types of preference variables only affects the current PowerShell session.

The second part of the puzzle is that when the <em>OutVariable</em> parameter is used to store the results in a variable, it returns the results and stores them in the specified variable. To perform this functionality silently, simply store the results of the command in a variable instead of using the <em>OutVariable</em> parameter.

Set the global <em>$ProgressPreference</em> variable to silently continue (the default is continue), store the results of the command in a variable, and perform logic on the contents of the variable to determine if a reboot is needed.
<pre class="lang:ps decode:true">$ProgPref = $ProgressPreference
$ProgressPreference = 'SilentlyContinue'
$results = Enable-WindowsOptionalFeature -FeatureName Microsoft-Windows-Subsystem-Linux -Online -NoRestart
$ProgressPreference = $ProgPref
if ($results.RestartNeeded -eq $true) {
  Restart-Computer -Force
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl4b.jpg"><img class="alignnone size-full wp-image-16630" src="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl4b.jpg" alt="" width="859" height="152" /></a>

Last, but not least, the warning about the restart being suppressed needs to be silenced in the previous example. This is a scenario where there's no need to change the value of the global <em>$WarningPreference</em> variable because the command has a <em>WarningPreference</em> parameter that only affects this single command this one time.
<pre class="lang:ps decode:true">$ProgPref = $ProgressPreference
$ProgressPreference = 'SilentlyContinue'
$results = Enable-WindowsOptionalFeature -FeatureName Microsoft-Windows-Subsystem-Linux -Online -NoRestart -WarningAction SilentlyContinue
$ProgressPreference = $ProgPref
if ($results.RestartNeeded -eq $true) {
  Restart-Computer -Force
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl5b.jpg"><img class="alignnone size-full wp-image-16631" src="http://mikefrobbins.com/wp-content/uploads/2018/06/install-wsl5b.jpg" alt="" width="859" height="157" /></a>

As you've seen in this blog article, it seems as if something is missing from the <em>Enable-WindowsOptionalFeature</em> cmdlet when trying to automate the installation of Windows features with it, but when you know how to harness the power of PowerShell, there's almost always a solution to every problem.

µ