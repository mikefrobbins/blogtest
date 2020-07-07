---
ID: 2658
post_title: >
  Set SharePoint 2010 Outgoing E-Mail
  Settings with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/08/04/configure-sharepoint-2010-outgoing-email-settings-with-powershell/
published: true
post_date: 2011-08-04 07:30:47
---
Configure the outgoing email settings in a SharePoint 2010 Farm with PowerShell.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email1.png"><img class="alignnone size-full wp-image-2659" title="outbound-email1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email1.png" width="640" height="342" /></a>

Launch the PowerShell ISE. Import the SharePoint PowerShell Snap-in, modify the script below using your specific email server settings and then run the script.
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell

$SMTPSvr = 'mail.mikefrobbins.com'
$FromAddr = 'noreply@mikefrobbins.com'
$ReplyAddr = 'noreply@mikefrobbins.com'
$Charset = 65001

$CAWebApp = Get-SPWebApplication -IncludeCentralAdministration | Where { $_.IsAdministrationWebApplication }
$CAWebApp.UpdateMailSettings($SMTPSvr, $FromAddr, $ReplyAddr, $Charset)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email2.png"><img class="alignnone size-full wp-image-2660" title="outbound-email2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email2.png" width="640" height="195" /></a>

The outgoing email settings are now configured.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email3.png"><img class="alignnone size-full wp-image-2661" title="outbound-email3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/outbound-email3.png" width="640" height="341" /></a>

µ