---
ID: 5314
post_title: >
  Use PowerShell to Determine When Your
  DHCP Lease Expires
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/09/27/use-powershell-to-determine-when-your-dhcp-lease-expires/
published: true
post_date: 2012-09-27 07:30:10
---
This blog post applies to only Windows 8 and Windows Server 2012. Want to know how much time there is until your current DHCP leases expire?
<pre class="lang:ps decode:true">Get-NetIPAddress -PrefixOrigin Dhcp |
select InterfaceAlias , IPAddress, ValidLifetime</pre>
I have 23 hours, 47 minutes, and 46 seconds until my current DHCP lease expires on one of my network interfaces and 22 seconds more than that left on the other one.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/dhcp-lease-time01.jpg"><img class="alignnone size-full wp-image-5486" title="dhcp-lease-time01" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/dhcp-lease-time01.jpg" width="492" height="96" /></a>

Want to know the exact date and time of when they expire?
<pre class="lang:ps decode:true">Get-NetIPAddress -PrefixOrigin Dhcp |
select InterfaceAlias, IPAddress,
@{l="DHCP Expiration";e={(Get-Date).addticks($_.ValidLifetime.ticks)}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/dhcp-lease-time05.jpg"><img class="alignnone size-full wp-image-5503" title="dhcp-lease-time05" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/dhcp-lease-time05.jpg" width="542" height="109" /></a>

I haven't added any error handling to these scripts so if DHCP is not enabled on any of your network interfaces, the scripts will generate errors.

µ