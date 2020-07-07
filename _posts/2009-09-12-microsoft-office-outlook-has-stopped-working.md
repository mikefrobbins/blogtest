---
ID: 86
post_title: >
  Microsoft Office Outlook has stopped
  working
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/12/microsoft-office-outlook-has-stopped-working/
published: true
post_date: 2009-09-12 19:49:28
---
Problem:
I'm currently ruuning Windows 7 Ultimate Edition and Office 2007 Professional Plus. I receive "Microsoft Office Outlook has stopped working Windows is checking for a solution to the problem" just about everytime I use Outlook:

<a href="http://mikefrobbins.com/wp-content/uploads/2009/09/outlook_stopped_responding.jpg"><img class="alignnone size-full wp-image-85" src="http://mikefrobbins.com/wp-content/uploads/2009/09/outlook_stopped_responding.jpg" alt="outlook_stopped_responding" width="366" height="180" /></a>

Application Event Log Error Message:

<img class="alignnone size-full wp-image-87" title="outlook_stopped_responding_eventlog" src="http://mikefrobbins.com/wp-content/uploads/2009/09/outlook_stopped_responding_eventlog.jpg" alt="outlook_stopped_responding_eventlog" width="552" height="257" />

<em>Event ID: 1000 Task Category: (100) Level: Error Description: Faulting application name: OUTLOOK.EXE, version: 12.0.6504.5000, time stamp: 0x49e7f47e Faulting module name: olmapi32.dll, version: 12.0.6504.5000, time stamp: 0x49e7f423 Exception code: 0xc0000005 Fault offset: 0x00051a61 Faulting process id: 0xc1c Faulting application start time: 0x01ca340a023dfe6b Faulting application path: C:\Program Files (x86)\Microsoft Office\Office12\OUTLOOK.EXE Faulting module path: c:\progra~2\micros~1\office12\olmapi32.dll</em>

Solution:
Unknown. I have run "outlook /cleanviews" and scanpst.exe on my pst to fix all errors that were found with it. I am still experiencing this problem. If anyone has a solution for this problem, please comment.

Update - May 17th, 2010
After receiving the error "Outlook experienced a serious problem with the avg exchange extension add-in" and disabling the plugin,  I no longer experience issues with Outlook.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/avg_outlook_error.png"><img class="alignnone size-full wp-image-839" title="avg_outlook_error" src="http://mikefrobbins.com/wp-content/uploads/2010/05/avg_outlook_error.png" alt="" width="496" height="199" /></a>

µ