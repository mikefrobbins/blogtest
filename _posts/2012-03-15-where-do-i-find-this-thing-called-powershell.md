---
ID: 3408
post_title: >
  Where Do I Find This Thing Called
  PowerShell?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/03/15/where-do-i-find-this-thing-called-powershell/
published: true
post_date: 2012-03-15 07:30:45
---
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-1.png"><img class="alignright size-full wp-image-3409" title="ps101-1" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-1.png" alt="" width="217" height="192" /></a>If you’re running Windows 7, you already have PowerShell 2.0 installed. It’s located under “All Programs &gt; Accessories &gt; Windows PowerShell”. I’m running a 64bit version of Windows 7 so I have both 64bit and 32bit (x86) links in my start menu. I prefer the PowerShell ISE (Integrated Scripting Environment) as apposed to the PowerShell Console. I use the 64bit version and have never run into an issue where I needed to use the 32bit (x86) versions.

According to the <a href="http://support.microsoft.com/kb/968929" target="_blank">Microsoft Support website</a>, PowerShell 2.0 can be installed on Windows XP SP3 / Windows 2003 Server SP2 and newer operating systems. That web page also contains the links to download PowerShell 2.0 for these older operating systems.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-21111.png"><img class="alignleft size-full wp-image-3433" title="ps101-2111" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-21111.png" alt="" width="152" height="134" /></a>I use PowerShell often enough that I like to have it a little more accessible than having to drill down into the start menu each time I want to use it and I also don't want to clutter up my desktop with lots of icons. The solution for me was to create a shortcut on my taskbar by right clicking on the desired shortcut in the start menu and selecting "Pin to Taskbar".

This taskbar shortcut makes the PowerShell ISE much easier to access:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-3.png"><img class="alignnone size-full wp-image-3415" title="ps101-3" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-3.png" alt="" width="300" height="41" /></a>

The other tweak I recommend for this shortcut is to have it automatically "Run as Administrator" since PowerShell can't prompt you to elevate privileges if <a href="http://en.wikipedia.org/wiki/User_Account_Control" target="_blank">user access control</a> (UAC) is enabled. Hold down shift and right click on the taskbar shortcut, select properties &gt; advanced and check the "Run as Administrator" checkbox:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-4.png"><img class="alignnone size-full wp-image-3417" title="ps101-4" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-4.png" alt="" width="392" height="298" /></a>

If UAC is enabled on your machine, each time you open the PowerShell ISE using this shortcut you'll be prompted to start it with elevated user access control privileges. If you're logged into your machine as a non-admin account, you'll be prompted with a slightly different dialog box that also requires you to enter the credentials of an admin account.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-5.png"><img class="alignnone size-full wp-image-3418" title="ps101-5" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-5.png" alt="" width="464" height="244" /></a>

If the PowerShell Console or ISE is run without elevated UAC privileges (which is the default setting), you'll receive cryptic error messages when running a cmdlet that attempts to do something that requires elevated privileges:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-6.png"><img class="alignnone size-full wp-image-3419" title="ps101-6" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-6.png" alt="" width="640" height="76" /></a>

The same command runs without issue once the PowerShell Console or ISE is launched with elevated UAC privileges:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-7.png"><img class="alignnone size-full wp-image-3420" title="ps101-7" src="http://mikefrobbins.com/wp-content/uploads/2012/03/ps101-7.png" alt="" width="428" height="70" /></a>

µ