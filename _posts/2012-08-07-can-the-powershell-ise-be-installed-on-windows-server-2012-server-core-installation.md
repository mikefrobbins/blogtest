---
ID: 5090
post_title: >
  Can the PowerShell ISE be Installed on
  Windows Server 2012 Server Core
  Installation?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/07/can-the-powershell-ise-be-installed-on-windows-server-2012-server-core-installation/
published: true
post_date: 2012-08-07 07:30:37
---
I noticed a few days ago that the PowerShell ISE is an available feature on Windows Server 2012 Release Candidate when running the Server Core Installation (no GUI):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core1.jpg"><img class="alignnone size-full wp-image-5091" title="2012-ise-core1" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core1.jpg" alt="" width="582" height="134" /></a>

Does that mean the PowerShell ISE can run on Server Core in Windows Server 2012 unlike in Windows Server 2008 R2? Let's find out.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core2.jpg"><img class="alignnone size-full wp-image-5092" title="2012-ise-core2" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core2.jpg" alt="" width="607" height="119" /></a>

The installation takes a long time and then you're prompted to restart when it's complete:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core3.jpg"><img class="alignnone size-full wp-image-5093" title="2012-ise-core3" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core3.jpg" alt="" width="640" height="175" /></a>

Finally when the restart is finished and you log in, Server Manager will be loaded and you'll discover that you now have what's called "Minimum Server Interface":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core4.jpg"><img class="alignnone size-full wp-image-5094" title="2012-ise-core4" src="http://mikefrobbins.com/wp-content/uploads/2012/08/2012-ise-core4.jpg" alt="" width="640" height="278" /></a>

So the answer is no, the PowerShell ISE can't be installed on "Server Core" in 2012.

µ