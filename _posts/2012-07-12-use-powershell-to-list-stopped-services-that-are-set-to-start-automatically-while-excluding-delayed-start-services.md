---
ID: 4753
post_title: >
  Use PowerShell to List Stopped Services
  that are Set to Start Automatically
  While Excluding Delayed Start Services
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/12/use-powershell-to-list-stopped-services-that-are-set-to-start-automatically-while-excluding-delayed-start-services/
published: true
post_date: 2012-07-12 07:30:31
---
Have you ever had 67 emails about services on your servers being up and down from your monitoring solution? It's not a good feeling and those emails are only about the ones that monitoring is setup for. What other services could be stopped that aren't being monitored? Wouldn't you like a quick and easy way to check whether or not all of the services that are set to start automatically are actually running?

My first thought: Use the <em>Get-Service</em> cmdlet. Unfortunately it doesn't have a parameter for the Startup Type:
<pre class="lang:ps decode:true">Get-Service | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services1.jpg"><img class="alignnone size-full wp-image-4768" title="ps-services1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services1.jpg" width="640" height="421" /></a>

It's a little more difficult, but what about using <a href="http://en.wikipedia.org/wiki/Windows_Management_Instrumentation" target="_blank">WMI</a> via the <em>Get-WMIObject</em> cmdlet? Most of the WMI classes I've found useful start with win32 and I'm looking for a service class:
<pre class="lang:ps decode:true">Get-WmiObject -List win32*service*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services2.jpg"><img class="alignnone size-full wp-image-4769" title="ps-services2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services2.jpg" width="640" height="113" /></a>

What properties are available when using the WMI Win32_Service class? This looks promising because there's a StartMode parameter. The only problem is that the BITS service shown in the following image is set to Delayed Start and the StartMode is "Auto":
<pre class="lang:ps decode:true">Get-WmiObject -Class Win32_Service -Filter "name = 'bits'" |
Format-List * -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services3.jpg"><img class="alignnone size-full wp-image-4770" title="ps-services3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services3.jpg" width="640" height="538" /></a>

WMI can be used to retrieve a list of stopped services that are set to start automatically:
<pre class="lang:ps decode:true">Get-WmiObject -Class Win32_Service -Filter "state = 'stopped' and startmode = 'auto'" |
Select-Object name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services4.jpg"><img class="alignnone size-full wp-image-4771" title="ps-services4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services4.jpg" width="640" height="107" /></a>

The problem is that all of the services shown in the previous image except one are set to "Automatic (Delayed Start)" as shown in the example in the following image. This causes a sort of false positive because those services aren't necessarily suppose to be running:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services5.jpg"><img class="alignnone size-full wp-image-4772" title="ps-services5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services5.jpg" width="420" height="474" /></a>

The only way I've found to determine if a service is set to "DelayedAutoStart" is in the registry. This property does not exist for services that are not set to "DelayedAutoStart":
<pre class="lang:ps decode:true">Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services |
Where-Object {$_.property -contains "DelayedAutoStart"} |
Select-Object -First 1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services6.jpg"><img class="alignnone size-full wp-image-4774" title="ps-services6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services6.jpg" width="640" height="273" /></a>

Here's a list of all the services on my pc that are set to "DelayedAutoStart":
<pre class="lang:ps decode:true">Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services |
Where-Object {$_.property -contains "DelayedAutoStart"} |
Select-Object -ExpandProperty PSChildName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services7.jpg"><img class="alignnone size-full wp-image-4775" title="ps-services7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services7.jpg" width="571" height="292" /></a>

The first command retrieves a list of stopped services that are set to start automatically. The second one retrieves a list of services that are set to "DelayedAutoStart". Combine the two so the items returned by the first command that exist in the second one are not returned in the final results:
<pre class="lang:ps decode:true">(Get-WmiObject -Class Win32_Service -Filter "state = 'stopped' and startmode = 'auto'" |
 Select-Object -ExpandProperty name) |
Where-Object {(Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\Services |
 Where-Object {$_.property -contains "DelayedAutoStart"} |
 Select-Object -ExpandProperty PSChildName) -notcontains $_} |
Select-Object @{l='Computer Name';e={$env:computername}},
 @{l='Service Name';e={$_}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services81.jpg"><img class="alignnone size-full wp-image-4790" title="ps-services81" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services81.jpg" width="478" height="163" /></a>

For the final test, I'll run this script on 32 servers at the same time:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services91.jpg"><img class="alignnone size-full wp-image-4814" title="ps-services91" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services91.jpg" width="596" height="379" /></a>

There are 32 different server names contained in the servers.txt file and it took 3 seconds to complete. Now that's what I call efficiency and a lot less hassle than 67 emails to look at.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services101.jpg"><img class="alignnone size-full wp-image-4815" title="ps-services101" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-services101.jpg" width="467" height="282" /></a>

µ