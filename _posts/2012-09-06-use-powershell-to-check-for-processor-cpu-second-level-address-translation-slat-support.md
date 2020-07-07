---
ID: 5298
post_title: >
  Use PowerShell to Check for Processor
  (CPU) Second Level Address Translation
  (SLAT) Support
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/09/06/use-powershell-to-check-for-processor-cpu-second-level-address-translation-slat-support/
published: true
post_date: 2012-09-06 07:30:02
---
The <a href="http://technet.microsoft.com/en-us/library/hh857623.aspx" target="_blank">hardware requirements</a> for using Hyper-V to run virtual machines on a Windows 8 client states that a 64-bit system that has a processor (CPU) that supports Second Level Address Translation (SLAT) is required. A minimum of 4GB of RAM is also required. How do I know if my Processor (CPU) supports Second Level Address Translation?

You could do like most blogs on the Internet state and use <a href="http://technet.microsoft.com/en-us/sysinternals/cc835722.aspx" target="_blank">Coreinfo</a>:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-1.jpg"><img class="alignnone size-full wp-image-5301" title="slat-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-1.jpg" width="554" height="139" /></a>

You have two additional choices if you already have Windows 8 or Windows Server 2012 Installed. #1 - Run systeminfo.exe:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-2.jpg"><img class="alignnone size-full wp-image-5302" title="slat-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-2.jpg" width="546" height="71" /></a>

#2 - You could use this thing called <a href="http://technet.microsoft.com/en-us/library/bb978526.aspx" target="_blank">PowerShell</a> which is what all the cool kids are using these days:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName win32_processor -Property Name, SecondLevelAddressTranslationExtensions, VirtualizationFirmwareEnabled, VMMonitorModeExtensions |
Format-List Name, SecondLevelAddressTranslationExtensions, VirtualizationFirmwareEnabled, VMMonitorModeExtensions</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-3.jpg"><img class="alignnone size-full wp-image-5303" title="slat-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-3.jpg" width="607" height="151" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-4.jpg"><img class="alignnone size-full wp-image-5304" title="slat-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-4.jpg" width="640" height="143" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-5.jpg"><img class="alignnone size-full wp-image-5305" title="slat-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/09/slat-5.jpg" width="640" height="147" /></a>

µ