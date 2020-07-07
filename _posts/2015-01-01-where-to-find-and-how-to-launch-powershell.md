---
ID: 10985
post_title: 'Where to Find &#038; How to Launch PowerShell'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/01/01/where-to-find-and-how-to-launch-powershell/
published: true
post_date: 2015-01-01 07:30:18
---
Happy New Year! What better way to start the new year out than by setting some goals and what better goal to set than to learn PowerShell this year.

Let's start out at ground zero by learning how to launch PowerShell. On Windows 8 or 8.1, simply start typing the word PowerShell on the Metro interface screen:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1a.jpg"><img class="alignnone size-full wp-image-10986" src="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1a.jpg" alt="ps-day1a" width="919" height="389" /></a>

Maybe you're still running Windows 7? If so, the shortcuts for PowerShell are located in the start menu under All Programs &gt; Accessories &gt; Windows PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1b.jpg"><img class="alignnone size-full wp-image-10987" src="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1b.jpg" alt="ps-day1b" width="423" height="526" /></a>

Notice in the previous example that there are four different shortcuts for Windows PowerShell. This is because the computer is running a 64bit operating system. The shortcuts with the (x86) suffix are for the 32bit versions of PowerShell. For now, forget that those exist and stick to the shortcuts without the (x86) suffix unless you have a specific reason for using the 32bit versions.

The "Windows PowerShell" shortcut will launch the PowerShell console and that's what I'll be demonstrating during my next few blog articles. The "Windows PowerShell ISE" shortcut will launch the PowerShell Integrated Scripting Environment.

Go ahead and launch PowerShell and you should have a PowerShell console window that looks similar to this one:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1c.jpg"><img class="alignnone size-full wp-image-10994" src="http://mikefrobbins.com/wp-content/uploads/2014/12/ps-day1c.jpg" alt="ps-day1c" width="877" height="163" /></a>

Note that the title bar in the previous example says "Windows PowerShell". This means that the PowerShell console is not running as an administrator. This can create problems where you'll receive errors when attempting to run commands that need elevated privileges such as trying to stop a service. Most programs prompt the user with a UAC (User Access Control) prompt but PowerShell is unable to participate in UAC.

It's because of this that I recommend running PowerShell as an administrator by right clicking on the PowerShell shortcut and selecting "Run as Administrator" when launching the PowerShell console:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day1e.jpg"><img class="alignnone size-full wp-image-11005" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day1e.jpg" alt="ps-day1e" width="877" height="163" /></a>

Notice the title bar now says "Administrator: Windows PowerShell". Keep in mind that you should be running PowerShell as a local admin that is a domain user if your computer is a member of an Active Directory domain (definitely not as a domain admin and not as a user in your domain that has elevated privileges).

<hr />

If you enjoy reading my blog articles and you happen to be in the Cincinnati area, I'll be presenting a session on "PowerShell: From Beginner to Infinity &amp; Beyond!" for the <a href="http://www.cinpa.org/meeting-topic/2014/11/17/power-shell-january-7-2105" target="_blank">Cincinnati Networking Professionals Association</a> on January 7th.

µ