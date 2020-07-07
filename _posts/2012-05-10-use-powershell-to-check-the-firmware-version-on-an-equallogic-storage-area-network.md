---
ID: 4028
post_title: >
  Use PowerShell to Check the Firmware
  Version on an EqualLogic Storage Area
  Network
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/10/use-powershell-to-check-the-firmware-version-on-an-equallogic-storage-area-network/
published: true
post_date: 2012-05-10 07:30:38
---
I received an email recently stating that I hadn't downloaded the most recent version of firmware for multiple EqualLogic Storage Area Networks that I support. What's the easiest way to check what firmware version an EqualLogic SAN is running? PowerShell of Course!

You'll need the EqlPSTools PowerShell Module which is part of the EqualLogic Host Integration Tools (HIT) kit for Microsoft that can be downloaded from the <a href="http://support.dell.com/support/topics/global.aspx/support/enterprise_support/en/equal_logic?c=us&amp;l=en&amp;s=gen" target="_blank">EqualLogic support site</a>. Once this module has been installed, you'll be able to check the firmware version on your EqualLogic SAN using the following PowerShell script:
<pre class="lang:ps decode:true">$GrpAddr = "192.168.10.100"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Get-EqlMember -GroupAddress $GrpAddr |
Select-Object MemberName, FirmwareVersion | Format-Table -AutoSize
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/san-fw-ver1.png"><img class="alignnone size-full wp-image-4033" title="san-fw-ver1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/san-fw-ver1.png" width="529" height="247" /></a>

µ