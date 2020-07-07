---
ID: 6111
post_title: >
  PowerShell Version 3 Installation
  Failure on Windows Server 2008 R2 Server
  Core Installation (no-GUI)
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/
published: true
post_date: 2012-11-29 07:30:10
---
You have a server which runs the Windows Server 2008 R2 with SP1 operating system that was installed using the "Server Core Installation" option (no-GUI):

<a href="http://mikefrobbins.com/2012/05/03/is-powershell-2-0-installed-by-default-on-windows-server-2008-r2/ps2-2008r2core01/" rel="attachment wp-att-4066"><img class="alignnone size-full wp-image-4066" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core01.png" height="480" width="640" /></a>

You've given this server a name, added it to the domain, configured the IP address settings, and configured options 1 -3 in the "Configure Remote Management" portion of sconfig as shown in the following image:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure1/" rel="attachment wp-att-6112"><img class="alignnone size-full wp-image-6112" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure1.png" height="503" width="547" /></a>

PowerShell version 2 works fine on the server, but you've been tasked with loading PowerShell version 3 on it. You've downloaded and installed the specific version of the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=22833" target="_blank">.NET Framework 4.0 that's for Server Core from the Microsoft Download Center</a>:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure2/" rel="attachment wp-att-6113"><img class="alignnone size-full wp-image-6113" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure2.png" height="241" width="522" /></a>

You've also downloaded and installed the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=34595" target="_blank">Windows Management Framework 3.0 from the Microsoft Download Center</a> (<em>Windows6.1-KB2506143-x64.msu</em>). This installer includes PowerShell 3.0, WMI, and WinRM:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure3/" rel="attachment wp-att-6114"><img class="alignnone size-full wp-image-6114" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure3.png" height="154" width="419" /></a>

This installation required a restart. Upon restarting the server, you receive the following message: <span style="color:#ff0000;"><em>"Failure configuring Windows updates Reverting changes. Do not turn off your computer."</em></span>

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure4/" rel="attachment wp-att-6115"><img class="alignnone size-full wp-image-6115" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure4.png" height="175" width="548" /></a>

What's the cause of this problem? Simply not reading the instructions for installing the .NET Framework 4.0 on Server Core:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure4a/" rel="attachment wp-att-6135"><img class="alignnone size-full wp-image-6135" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure4a.png" height="417" width="527" /></a>

If you followed those instructions, two features; <em>WoW64-NetFx2-Support</em> and <em>WoW64-NetFx2</em> would be enabled in addition to the two features that were already enabled. I'm going to go ahead and enable the <em>WoW64-PowerShell</em> feature also since it's listed as being necessary in the documentation to manage a 2008 R2 Server Core machine from the new Windows Server 2012 Server Manager interface:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure5/" rel="attachment wp-att-6116"><img class="alignnone size-full wp-image-6116" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure5.png" height="159" width="621" /></a>

Enabling the one parent feature will automatically enable the two child features:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure6/" rel="attachment wp-att-6117"><img class="alignnone size-full wp-image-6117" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure6.png" height="108" width="637" /></a>

Verifying the features were enabled:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure7/" rel="attachment wp-att-6118"><img class="alignnone size-full wp-image-6118" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure7.png" height="139" width="617" /></a>

Enabling those features allows the Windows Management Framework 3.0 installation to complete successfully. I've re-run the installation of Windows Management Framework 3.0 and restarted the server. PowerShell version 3.0 was successfully installed:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure8/" rel="attachment wp-att-6119"><img class="alignnone size-full wp-image-6119" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure8.png" height="161" width="379" /></a>

In a future blog article, I'll cover the remainder of the necessary configuration to manage a Windows Server 2008 R2 machine from the new Windows Server 2012 Server Manager:

<a href="http://mikefrobbins.com/2012/11/29/powershell-version-3-installation-failure-on-windows-server-2008-r2-server-core-installation-no-gui/sc-ps3-failure9/" rel="attachment wp-att-6139"><img class="alignnone size-full wp-image-6139" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/sc-ps3-failure9.png" height="201" width="640" /></a>

µ