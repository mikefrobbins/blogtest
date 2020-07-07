---
ID: 5188
post_title: >
  How to Install the Exchange 2010
  Management Tools on Windows 8 RTM
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/16/how-to-install-the-exchange-2010-management-tools-on-windows-8-rtm/
published: true
post_date: 2012-08-16 20:14:23
---
This is definitely unsupported so be sure to read the disclaimer on the right side of this blog before continuing. Read this blog article completely before attempting this process.

You've just reloaded your work computer with Windows 8 RTM that you downloaded from MSDN or TechNet and you need to install the Exchange Server 2010 Management Tools so you can manage your Exchange server without having to log into it <del>whether it be with the GUI or</del> with PowerShell. In this example, I run setup.exe from a copy of Exchange 2010 with Service Pack 2, clicked through a couple of screens taking all the defaults and on the following screen, select "Custom Exchange Server Installation". Then select next:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools1.jpg"><img class="alignnone size-full wp-image-5189" title="win8-exchange2010tools1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools1.jpg" width="637" height="556" /></a>

You'll only need the "Management Tools":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools2.jpg"><img class="alignnone size-full wp-image-5190" title="win8-exchange2010tools2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools2.jpg" width="637" height="556" /></a>

The prerequisite checks will fail stating that Windows 8 is not supported:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools.jpg"><img class="alignnone size-full wp-image-5186" title="win8-exchange2010tools" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools.jpg" width="637" height="556" /></a>

You can use PowerShell or RegEdit to modify the CurrentVersion setting in the registry from 6.2 to 6.1 which tells Windows 8, you're not really Windows 8:
<pre class="lang:ps decode:true">(Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion").CurrentVersion
Set-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" -Name CurrentVersion -Value 6.1
(Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion").CurrentVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools-fix.jpg"><img class="alignnone size-full wp-image-5187" title="win8-exchange2010tools-fix" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools-fix.jpg" width="640" height="67" /></a>

You'll need to manually enable the "IIS 6 Management Console" and "IIS 6 Metabase Compatibility" features otherwise the installation will fail at this point due to those missing. Once all of those changes have been made, the "Readiness Checks" will complete:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools3.jpg"><img class="alignnone size-full wp-image-5191" title="win8-exchange2010tools3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools3.jpg" width="637" height="556" /></a>

The installation finishes without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools4.jpg"><img class="alignnone size-full wp-image-5192" title="win8-exchange2010tools4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools4.jpg" width="637" height="556" /></a>

Once the installation is complete, change the CurrentVersion setting back to 6.2. PowerShell is my tool of choice and I like problems that give me an excuse to use it.
<pre class="lang:ps decode:true">Set-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" -Name CurrentVersion -Value 6.2
(Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion").CurrentVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools5.jpg"><img class="alignnone size-full wp-image-5193" title="win8-exchange2010tools5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools5.jpg" width="640" height="41" /></a>

This process circumvents a safety feature that I'm sure was put in place for a good reason so you've been warned. Don't blame me when your computer crashes and burns.

<span style="text-decoration: underline;">Update 8/21/12
</span>Based on the comments I've received on this blog, I've verified that there is an issue with the Exchange Management Console (GUI). You're unable to expand the tree beyond the point shown in the following image to manage the Exchange server from Windows 8:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools6.jpg"><img class="alignnone size-full wp-image-5261" title="win8-exchange2010tools6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/win8-exchange2010tools6.jpg" width="221" height="133" /></a>

Based on this, the only reason to attempt this install is for the PowerShell snap-ins.

µ