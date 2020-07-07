---
ID: 12860
post_title: >
  StartType property added to Get-Service
  in PowerShell version 5 build 10586 on
  Windows 10 version 1511
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/12/17/starttype-property-added-to-get-service-in-powershell-version-5-build-10586-on-windows-10-version-1511/
published: true
post_date: 2015-12-17 07:30:11
---
If you've used PowerShell for more than 5 minutes, then you probably have some experience with the <em>Get-Service</em> cmdlet. As you could have guessed if you didn't already know, the <em>Get-Service</em> cmdlet retrieves information about Windows services.

Depending on what you're trying to accomplish, that particular cmdlet could leave a lot to be desired. Want to know if a service is running? No problem. Want to know what the startup type is for one or more services? Sorry, you can't accomplish that task with <em>Get-Service</em>.

Notice that <em>Get-Service</em> in PowerShell version 5 build 10240 on Windows 10 (and all prior operating systems and PowerShell versions) doesn't include a StartType property:
<pre class="lang:ps decode:true">Get-Service -Name Dnscache | Select-Object -Property *</pre>
<img class="alignnone size-full wp-image-12862" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start1a.jpg" alt="trigger-start1a" width="859" height="406" />

This wasn't due to a problem with the <em>Get-Service</em> cmdlet as it simply returns what the .NET Framework provides:
<pre class="lang:ps decode:true">[System.ServiceProcess.ServiceController]::GetServices() |
Where-Object Name -eq Dnscache |
Select-Object -Property *</pre>
<img class="alignnone size-full wp-image-12867" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start4a.jpg" alt="trigger-start4a" width="859" height="322" />

That meant that you had to resort to querying WMI to determine if a service is set to start automatically, manual, disabled, etc. This can be accomplished with either the <em>Get-WMIObject</em> or <em>Get-CimInstance </em>cmdlet:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_Service -Filter "Name = 'Dnscache'" -Property *</pre>
<img class="alignnone size-full wp-image-12863" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start2a.jpg" alt="trigger-start2a" width="859" height="576" />

If you're running Windows 10 version 1511 (the updated version released in November of 2015), then you're in luck because that particular version of PowerShell version 5 (build 10586) adds a StartType property to <em>Get-Service</em>:
<pre class="lang:ps decode:true">Get-Service -Name Dnscache | Select-Object -Property *</pre>
<img class="alignnone size-full wp-image-12864" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start3a.jpg" alt="trigger-start3a" width="859" height="419" />

Once again, they are simply returning what the .NET Framework provides and this addition property is now included:
<pre class="lang:ps decode:true ">[System.ServiceProcess.ServiceController]::GetServices() |
Where-Object Name -eq Dnscache |
Select-Object -Property *</pre>
<img class="alignnone size-full wp-image-12868" src="http://mikefrobbins.com/wp-content/uploads/2015/12/trigger-start5a.jpg" alt="trigger-start5a" width="859" height="333" />

As I've previously written, <a href="http://mikefrobbins.com/2015/09/04/powershell-delayedautostart-property-added-to-the-win32_service-wmi-class-in-windows-10-rtm/" target="_blank">PowerShell version 5 adds a property to the Win32_Service WMI class</a> that allows you to determine if a service is set to start automatically with a delayed start. If you're running a previous version of PowerShell, you'll have to query the registry for that info as demonstrated in <a href="http://blogs.technet.com/b/heyscriptingguy/archive/2014/07/31/exclude-delayed-start-services-when-checking-status-with-powershell.aspx" target="_blank">this Hey, Scripting Guy! Blog article</a>.

Stay tuned next week when I'll demonstrate how to determine if a service is set to start automatically with a trigger start. This is more difficult to determine than the delayed start ones and unfortunately there's no additions to newer operating systems or PowerShell versions to make it simpler.

µ