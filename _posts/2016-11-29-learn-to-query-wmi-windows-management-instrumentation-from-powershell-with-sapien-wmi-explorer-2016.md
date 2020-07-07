---
ID: 14776
post_title: >
  Learn to Query WMI (Windows Management
  Instrumentation) from PowerShell with
  SAPIEN WMI Explorer 2016
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/11/29/learn-to-query-wmi-windows-management-instrumentation-from-powershell-with-sapien-wmi-explorer-2016/
published: true
post_date: 2016-11-29 07:30:43
---
One of the reasons I like mentoring others and teaching them how to use PowerShell is that I spent the first third of my career pointing and clicking in the GUI. Before PowerShell was released, I had taken a three day MOC (Microsoft Official Curriculum) course on VBScript and two more days on WMI (Windows Management Instrumentation). I wrote a few VBScripts over the years but never really had a desire to learn it like I have PowerShell. I could write a VBScript if required but it seemed too developer oriented than for the average IT Pro.

The company I was working for at the time was an early adopter to Exchange 2007 and there were a lot of things that couldn't be done in the GUI (Graphical User Interface). That was back in the PowerShell 1.0 days and I was the dangerous script copy and paster back then. I purchased the PowerShell Step by Step 1.0 book by Ed Wilson but sadly never read it. I had Ed Wilson sign it years later and told him it was the book I should have read.

With the release of PowerShell 2.0, I could see that Microsoft was really heading somewhere with PowerShell. I started writing my own one-liners but often times went back to the GUI if the task couldn't be accomplished with a one-liner. I purchased (and read) the original Learn Windows PowerShell in a Month of Lunches book by Don Jones. The first time I started reading it, I made it to somewhere in chapter 6 before the book started querying WMI with PowerShell. At that point, I felt lost and put the book away for a month or two. I eventually started reading it again from the beginning and made it all the way through (that series of books really is great).

While both WMI and CIM seem simple now, they didn't initially for me. This is one of the reasons I like <a href="https://www.sapien.com/software/wmiexplorer" target="_blank">SAPIEN's WMI Explorer product</a>. It allows you to explore WMI namespaces (A), classes (B), and properties (C) from a GUI interface and provides help in the form of PowerShell commands that use WMI and CIM (D). It also provides the VBScript command to accomplish the same task in case you come from a VBScript background (D).

<img class="alignnone size-full wp-image-14779" src="http://mikefrobbins.com/wp-content/uploads/2016/11/wmi-explorer1a.png" alt="wmi-explorer1a" width="859" height="1010" />

Clicking on the "Run this" link in the Help section runs the specified command:

<img class="alignnone size-full wp-image-14780" src="http://mikefrobbins.com/wp-content/uploads/2016/11/wmi-explorer2a.png" alt="wmi-explorer2a" width="859" height="439" />

The PowerShell command and the results are returned right from within WMI Explorer:

<img class="alignnone size-full wp-image-14781" src="http://mikefrobbins.com/wp-content/uploads/2016/11/wmi-explorer3a.png" alt="wmi-explorer3a" width="859" height="462" />

There's certainly a lot more that WMI Explorer does, but this is my favorite feature since it helps IT Pro's who are use to pointing and clicking in a GUI transition to using PowerShell by allowing them to select the WMI namespace and class from within a graphical user interface and show them not only the results of the query, but the PowerShell command that was run to produce the results.

A fully functional <a href="https://www.sapien.com/software/wmiexplorer" target="_blank">45 day evaluation version of WMI Explorer 2016</a> can be downloaded from <a href="https://www.sapien.com/" target="_blank">the SAPIEN Technologies website</a>.

µ