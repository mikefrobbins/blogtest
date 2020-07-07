---
ID: 5669
post_title: 'When Trying to Install PowerShell Version 3.0 You Receive the Message: &#8220;The Update is Not Applicable to Your Computer&#8221;'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/03/when-trying-to-install-powershell-version-3-0-you-receive-the-message-the-update-is-not-applicable-to-your-computer/
published: true
post_date: 2012-11-03 08:30:03
---
You've decided to install PowerShell version 3.0 on your computer. Your computer meets the requirement of running Windows 7 with Service Pack 1, Windows Server 2008 with Service Pack 2, or Windows Server 2008 R2 with Service Pack 1. If you're running Windows 8 or Windows Server 2012, you already have PowerShell version 3.0 installed.

There are several different ways to check the operating system version and service pack level. In the following screenshot, I've run "winver.exe" and I can see that this computer is running Windows 7 Enterprise Edition and it has Service Pack 1 installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error11.png"><img class="alignnone size-full wp-image-5670" title="ps3-install-error11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error11.png" height="224" width="469" /></a>

The only issue with checking the operating system version with "winver.exe" is that it doesn't show if the operating system is 32 or 64 bit which you'll need to know in order to download the appropriate version of PowerShell 3.0 which is part of the "Windows Management Framework 3.0". Checking the version through the control panel shows this information though:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error12.png"><img class="alignnone size-full wp-image-5671" title="ps3-install-error12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error12.png" height="371" width="640" /></a>

You've verified your operating system meets the requirements and you've headed over to the Microsoft download center and located the "<a href="http://www.microsoft.com/en-us/download/details.aspx?id=34595" target="_blank">Windows Management Framework 3.0</a>". Since the machine I'm installing this on is running a 64 bit version of Windows 7, I'll need the file shown below with the red box around it:

<a href="http://www.microsoft.com/en-us/download/details.aspx?id=34595" target="_blank"><img class="alignnone size-full wp-image-5672" title="ps3-install-error13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error13.png" height="396" width="640" /></a>

You've downloaded the file and when attempting to install it, you receive the error: "The Update is not applicable to your computer.":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error14.png"><img class="alignnone size-full wp-image-5673" title="ps3-install-error14" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error14.png" height="296" width="561" /></a>

This error is due to not having the "<a href="http://www.microsoft.com/en-us/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>" installed. Download and install the .NET Framework 4 and then the installation will complete without issue. The .NET Framework 4 can be downloaded from the Microsoft Download Center:

<a href="http://www.microsoft.com/en-us/download/details.aspx?id=17851" target="_blank"><img class="alignnone size-full wp-image-5725" title="ps3-install-error16" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error16.png" height="276" width="640" /></a>

This is the web installation version. There's a full version if you have multiple computers or a computer without an Internet connection to install it on. It is also available as a Windows update. There's a separate version of the .NET Framework 4 if you're installing it on a server running the core installation (no-GUI) version of the operating system.

<span style="text-decoration:underline;">Update 11/4/12:</span>
Note that you need the full version of the .NET framework 4, the client profile version as shown in the following image is not sufficient and will also cause the same error message as not having the .NET framework 4 installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error18.png"><img class="alignnone size-full wp-image-5734" title="ps3-install-error18" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error18.png" height="276" width="640" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/ps3-install-error17.png"><img class="alignnone size-full wp-image-5729" title="ps3-install-error17" alt="" /></a>µ