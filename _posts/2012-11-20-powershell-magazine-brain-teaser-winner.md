---
ID: 6043
post_title: PowerShell Magazine Brain Teaser Winner!
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/20/powershell-magazine-brain-teaser-winner/
published: true
post_date: 2012-11-20 07:30:11
---
I received notification yesterday from <a href="http://www.powershellmagazine.com/" target="_blank">PowerShell Magazine</a> that I had won last weeks PowerShell Brain Teaser to "<a href="http://www.powershellmagazine.com/2012/11/12/assign-a-list-of-processes-to-a-variable-and-output-it-to-the-console/" target="_blank">Assign a list of processes to a variable and output it to the console</a>". The prize for winning that particular brain teaser was an eBook version of the recently released "<a href="http://www.manning.com/jones3/" target="_blank">Learn Windows PowerShell 3 in a Month of Lunches, Second Edition</a>" book written by Don Jones and Jeffery Hicks:

<a href="http://www.manning.com/jones3/" target="_blank"><img class="alignnone size-thumbnail wp-image-6044" title="jones3_cover150" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/jones3_cover150.jpg?w=120" width="120" height="150" /></a>

This weeks brain teaser is "<a href="http://www.powershellmagazine.com/2012/11/19/find-a-list-of-all-ip-addresses-assigned-to-the-local-system/" target="_blank">Find a list of all IP addresses assigned to the local system</a>". An eBook version of the "<a href="http://www.packtpub.com/microsoft-windows-powershell-3-0-firstlook/book" target="_blank">Microsoft Windows PowerShell 3.0 First Look</a>" book written by Adam Driscoll is being given away to this weeks brain teaser winner:

<a href="http://www.packtpub.com/microsoft-windows-powershell-3-0-firstlook/book" target="_blank"><img class="alignnone size-thumbnail wp-image-5807" title="powershell3-firstlook" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/powershell3-firstlook.png?w=119" width="119" height="150" /></a>

Something cool that I stumbled on while working on this weeks break teaser is being able to use PowerShell to view the local and remote endpoints for active TCP connections:
<pre class="lang:ps decode:true">[Net.NetworkInformation.IPGlobalProperties]::GetIPGlobalProperties().GetActiveTcpConnections() | Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-endpoints1.png"><img class="alignnone size-full wp-image-6052" title="ps-endpoints1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps-endpoints1.png" width="640" height="249" /></a>

Had I not been trying to figure a way out to retrieve the IP address information within the guidelines specified in this weeks brain teaser, I never would have known this was possible so it's not about winning or losing, it's about learning by participating.

µ