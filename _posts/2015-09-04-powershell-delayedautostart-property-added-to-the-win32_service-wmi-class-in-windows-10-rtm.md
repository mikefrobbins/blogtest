---
ID: 12346
post_title: '#PowerShell: DelayedAutoStart Property added to the Win32_Service WMI Class in Windows 10 RTM'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/09/04/powershell-delayedautostart-property-added-to-the-win32_service-wmi-class-in-windows-10-rtm/
published: true
post_date: 2015-09-04 07:30:04
---
I recently discovered that Windows 10 adds a DelayedAutoStart property to the Win32_Service WMI Class:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_Service -Filter "Name = 'MapsBroker'" | Format-List -Property *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart1a.jpg"><img class="alignnone size-full wp-image-12347" src="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart1a.jpg" alt="win10-delayedautostart1a" width="859" height="506" /></a>

I've verified that this property does not exist on prior operating systems such as Windows 8.1 even when they're updated to the <a href="http://blogs.msdn.com/b/powershell/archive/2015/08/31/windows-management-framework-5-0-production-preview-is-now-available.aspx" target="_blank">production preview version of PowerShell version 5</a>.

I had written a Hey, Scripting Guy! Blog article on how to query the registry of remote machines to "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2014/07/31/exclude-delayed-start-services-when-checking-status-with-powershell.aspx" target="_blank">Exclude Delayed Start Services when Checking Status with PowerShell</a>" that shows how to retrieve the necessary information to accomplish this task on other OS's, but it's nice to see that Microsoft has finally made this information easier to retrieve.

Previously you would have only been able to narrow the services down to the ones that were set to start automatically and weren't running with the Win32_Service WMI class, but without a way of excluding the DelayedAutoStart ones short of querying the registry:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_Service -Filter "StartMode = 'Auto' and State != 'Running'" |
Select-Object -Property State, Name, DisplayName, DelayedAutoStart</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart2a.jpg"><img class="alignnone size-full wp-image-12348" src="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart2a.jpg" alt="win10-delayedautostart2a" width="859" height="193" /></a>

Now with this new property, the task of excluding the DelayedAutoStart ones is easy:
<pre class="lang:ps decode:true ">Get-CimInstance -ClassName Win32_Service -Filter "StartMode = 'Auto' and DelayedAutoStart = 'False' and State != 'Running'" |
Select-Object -Property State, Name, DisplayName, DelayedAutoStart</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart3a.jpg"><img class="alignnone size-full wp-image-12349" src="http://mikefrobbins.com/wp-content/uploads/2015/09/win10-delayedautostart3a.jpg" alt="win10-delayedautostart3a" width="859" height="157" /></a>

µ