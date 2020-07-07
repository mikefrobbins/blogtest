---
ID: 4663
post_title: >
  Want VM Reliability and Uptime? Hyper-V
  on Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/05/want-vm-reliability-and-uptime-hyper-v-on-server-core/
published: true
post_date: 2012-07-05 07:30:33
---
While at TechEd last month I heard two things that I've been preaching for a while. Server Core installation (no-GUI) is the recommended installation type beginning with Windows Server 2012. I've been saying this for a while when it comes to Windows Server 2008 R2.

Server Core: Reliability and Uptime

In each of three datacenters that I support there are multiple Hyper-V servers that run Windows Server 2008 R2 w/SP1 with the Core Installation (Server Core). These production servers have unbelievable uptime and reliability. Everything from mission critical line of business SQL database servers, to Exchange, and SharePoint are all virtualized (guest VM's) on these Hyper-V servers. These servers were brought down yesterday (July 4th) for maintenance and are only brought down about twice a year.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/hyperv-uptime-162.jpg"><img class="alignnone size-full wp-image-4664" title="hyperv-uptime-162" src="http://mikefrobbins.com/wp-content/uploads/2012/07/hyperv-uptime-162.jpg" alt="" width="640" height="221" /></a>

PowerShell: Learn it, love it, and want more of it.

The other thing I heard a lot of at TechEd was PowerShell, PowerShell, and more PowerShell. If you're going to do something more than once, do it once with PowerShell.

µ