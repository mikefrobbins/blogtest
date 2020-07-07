---
ID: 9689
post_title: >
  Checking and Setting the Start Mode of a
  Windows Service with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/17/checking-and-setting-the-start-mode-of-a-windows-service-with-powershell/
published: true
post_date: 2014-04-17 07:30:23
---
Working with Services in PowerShell is probably one of the first tasks you'll undertake as a PowerShell newbie. Recently I needed to disable a few Windows services on a server and I'm a big believer in being able to get back to where you started so the first thing I set out to accomplish was to find out what the start mode was of the services I needed to change.

My demo machine doesn't have those particular services I referenced so the BITS service will be used for the demonstration in this scenario:
<pre class="lang:ps decode:true">Get-Service -Name BITS</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_19-14-48.png"><img class="alignnone size-full wp-image-9690" alt="2014-04-13_19-14-48" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_19-14-48.png" width="877" height="133" /></a>

By default only a few properties are displayed, but like most things in PowerShell, there's more properties available than are returned by default. I'll simply pipe the previous command to the <em>Select-Object</em> cmdlet, specify the <em>-Properties</em> parameter and use the wildcard character (asterisk) to return all properties and their values for the BITS service:
<pre class="lang:ps decode:true">Get-Service -Name BITS | Select-Object -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_19-21-23.png"><img class="alignnone size-full wp-image-9691" alt="2014-04-13_19-21-23" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_19-21-23.png" width="877" height="300" /></a>

As you can see in the previous results, there's still isn't a StartMode parameter. You can certainly change the StartMode of a service from within PowerShell with the <em>Set-Service</em> cmdlet, so why can't you check to see what the StartMode is from within PowerShell?

It can be done in WMI and PowerShell has cmdlets for accessing WMI. It's not really PowerShell's fault that it doesn't natively display the StartMode property though. You see, PowerShell runs on the .Net Framework and the <em>Get-Service</em> cmdlet returns the same properties as the underlying .Net Framework class which that cmdlet uses:
<pre class="lang:ps decode:true">Add-Type -AssemblyName 'System.ServiceProcess'
[System.ServiceProcess.ServiceController]::GetServices() |
Where-Object Name -eq BITS |
Select-Object -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-00-49.png"><img class="alignnone size-full wp-image-9693" alt="2014-04-13_20-00-49" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-00-49.png" width="877" height="348" /></a>

The previous results should look familiar (they're the same results as <em>Get-Service</em>).

We could use <em>Get-CimInstance</em> or <em>Get-WMIObject</em> to access WMI, specifically the Win32_Service class to see the StartMode property and its value for the BITS service:
<pre class="lang:ps decode:true">Get-WmiObject -Class Win32_Service -Filter "Name = 'BITS'" |
Select-Object -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-04-32.png"><img class="alignnone size-full wp-image-9694" alt="2014-04-13_20-04-32" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-04-32.png" width="877" height="643" /></a>

As you can see in the previous command, the StartMode for the BITS service is Manual.

I'm now going to change the StartMode for the BITS service to Automatic:
<pre class="lang:ps decode:true">Set-Service -Name BITS -StartupType Automatic</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-07-16.png"><img class="alignnone size-full wp-image-9695" alt="2014-04-13_20-07-16" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-07-16.png" width="877" height="66" /></a>

No error, but did the previous command do anything? You can make the previous command return the service that was changed by adding the <em>-PassThru</em> parameter:
<pre class="lang:ps decode:true">Set-Service -Name BITS -StartupType Automatic -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-09-23.png"><img class="alignnone size-full wp-image-9696" alt="2014-04-13_20-09-23" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-09-23.png" width="877" height="135" /></a>

I'll now double check to make sure the StartMode was indeed changed and this time I'll use the <em>Get-CimInstance</em> cmdlet which was introduced in PowerShell version 3 and can also be used to access WMI:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_Service -Filter "Name = 'BITS'" |
Select-Object -Property Name, StartMode</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-12-38.png"><img class="alignnone size-full wp-image-9697" alt="2014-04-13_20-12-38" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-13_20-12-38.png" width="877" height="158" /></a>

µ