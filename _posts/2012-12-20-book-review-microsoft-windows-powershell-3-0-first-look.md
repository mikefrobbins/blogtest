---
ID: 6216
post_title: 'Book Review: Microsoft Windows PowerShell 3.0 First Look'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/12/20/book-review-microsoft-windows-powershell-3-0-first-look/
published: true
post_date: 2012-12-20 07:30:13
---
About a week ago I started reading the “<a href="http://www.packtpub.com/microsoft-windows-powershell-3-0-firstlook/book" target="_blank">Microsoft Windows PowerShell 3.0 First Look</a>” book written by <a href="http://csharpening.net/" target="_blank">Adam Driscoll</a> and published by <a href="http://www.packtpub.com/" target="_blank">Packt</a>.

<a href="http://www.packtpub.com/microsoft-windows-powershell-3-0-firstlook/book" target="_blank" rel="attachment wp-att-6217"><img class="alignnone size-full wp-image-6217" alt="PowerShell 3 First Look" src="http://mikefrobbins.com/wp-content/uploads/2012/12/powershell-3-first-look.png" width="160" height="203" /></a>

One of the things I like about this book is the ability to read it in about a week since it’s only seven chapters long. In my opinion, its intended audience is people who are already somewhat familiar with PowerShell version 2 that are looking for a way to quickly get up to speed on the new features in version 3. Although I was already familiar with many of the new version 3 features covered in this book, what I was most impressed with was the depth that was gone into for each topic.

In chapter 1, the book starts off with a new PowerShell version 3 feature that Adam calls a transitional cmdlet that although useful to anyone, can be especially useful to someone transitioning from primarily using the GUI to using PowerShell. Next, the changes to the help system in version 3 are covered which sounds simple, but this book dives deeper into this subject and covers details that I haven't seen covered anywhere else. There are several caveats to the new module auto-discovery feature that I was unaware of which are covered in this chapter as well.

Chapter 2 covers the new simplified syntax for the <em>Where-Object</em> and <em>ForEach-Object</em> cmdlets. There are a few new parameters for <em>Where-Object</em> in version 3 that I learned about. This chapter also covers the enhancements to tab completion and <em>Get-ChildItem</em>.

I’ll quote one of the sentences from chapter 3, it’s my favorite one from the entire book: “<span style="color:#0000ff;"><em>With the power of .NET hiding behind it, PowerShell allows an everyday administrator to develop their own solutions without having to involve a developer</em></span>”. This chapter covers the new PowerShell cmdlets that allow you to setup scheduled tasks using PowerShell. This chapter goes on to cover the specifics of delegated administration in detail which is the ability to allow users to run remote PowerShell sessions using alternate credentials without the user having to know what the alternate credentials are (very cool). Who knew that you could use the <em>Restart-Computer</em> cmdlet with different communication protocols in PowerShell version 3?

PowerShell Workflows are covered in chapter 4. One of the great things about the way this book is written is you could skip an entire section or chapter if something such as workflows aren't of interest to you right now and none of the other chapters build on that information so you won’t miss out on anything else.

In chapter 5, the new CIM (<a href="http://en.wikipedia.org/wiki/Common_Information_Model_(computing)" target="_blank">Common Information Model</a>) cmdlets are covered.

PowerShell Web Access, one of my favorite new version 3 features in Windows Server 2012, is covered in chapter 6; this includes the installation, configuration, authorization of users, and the ins and outs of actually using PowerShell Web Access. The many enhancements to the ISE (Integrated Scripting Environment) in PowerShell version 3 are covered, here are a couple of ISE tips that I learned in this chapter: (1) Use Cntl + Spacebar to force intellisense to open and (2) use Cntl + J to show the snippet menu.

Chapter 7 covers the new PowerShell Modules and Cmdlets in Windows 8 and Windows Server 2012.

There’s much, much more PowerShell version 3 information covered in this book, too much to try to list in this review and listing all of it would spoil the fun of reading the book. I recommend this book for anyone who is already somewhat familiar with PowerShell version 2 and looking for a way to quickly get up to speed on the new features in PowerShell version 3. At $14.99 for the <a href="http://www.packtpub.com/microsoft-windows-powershell-3-0-firstlook/book" target="_blank">ebook version</a>, it's a no-brainer.

µ