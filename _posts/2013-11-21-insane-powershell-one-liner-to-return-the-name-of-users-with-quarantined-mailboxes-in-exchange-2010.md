---
ID: 8524
post_title: >
  Insane PowerShell One-Liner to Return
  the Name of Users with Quarantined
  Mailboxes in Exchange 2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/21/insane-powershell-one-liner-to-return-the-name-of-users-with-quarantined-mailboxes-in-exchange-2010/
published: true
post_date: 2013-11-21 07:30:23
---
Have you ever had any mailboxes on your Exchange 2010 server marked as being quarantined? According to this <a href="http://technet.microsoft.com/en-us/library/bb331958(EXCHG.140).aspx" target="_blank">TechNet article</a>, Exchange will isolate or quarantine any mailboxes that it detects as being poisoned. That TechNet article goes on to tell you how you can drill down in the registry on the Exchange Server to locate the GUID of the quarantined mailboxes. I don't know about you but using the GUI to dig around in the registry is not what I call fun so I decided to use PowerShell to accomplish that task.

If you're not running this from the Exchange Management Shell, you'll first need to load the Exchange 2010 PowerShell Snapin:
<pre class="lang:ps decode:true">Add-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction SilentlyContinue</pre>
Here's an insane PowerShell one liner that will do that dirty work for you and translate the GUID's that are contained in the registry to the actual name of the user's mailbox:
<pre class="wrap:false lang:ps decode:true">Get-ChildItem -Path HKLM:\SYSTEM\CurrentControlSet\services\MSExchangeIS\$env:COMPUTERNAME\ |
Where-Object {$_.pschildname -ne 'logstate' -and $_.pschildname -notlike 'public*'} |
Select-Object -ExpandProperty pschildname |
ForEach-Object {
    Get-ChildItem -Path HKLM:\SYSTEM\CurrentControlSet\services\MSExchangeIS\$Env:COMPUTERNAME\$_\QuarantinedMailboxes |
    Select-Object -ExpandProperty pschildname |
    Get-Mailbox |
    Select-Object -Property name                         
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/quarantined-mailbox-oneliner.png"><img class="alignnone size-full wp-image-8564" alt="quarantined-mailbox-oneliner" src="http://mikefrobbins.com/wp-content/uploads/2013/11/quarantined-mailbox-oneliner.png" width="748" height="266" /></a>

As the previously referenced TechNet article states, these mailboxes are quarantined for a certain amount of time so these registry keys will only contain a value for a specific amount of time (six hours by default or whatever MailboxQuarantineDurationInSeconds is set to).

If the one liner is too insane for you, here's a PowerShell script that does the same thing except it sends an email to you with the results if there are any:
<pre class="wrap:false lang:ps decode:true">Add-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010 -ErrorAction SilentlyContinue

$databases = Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\services\MSExchangeIS\$env:COMPUTERNAME\ |
             Where-Object {$_.pschildname -ne 'logstate' -and $_.pschildname -notlike 'public*'} |
             Select-Object -ExpandProperty pschildname

$corruptmailboxes = @()

foreach ($database in $databases) {
    $corruptmailboxes += Get-ChildItem HKLM:\SYSTEM\CurrentControlSet\services\MSExchangeIS\$Env:COMPUTERNAME\$database\QuarantinedMailboxes |
                         Select-Object -ExpandProperty pschildname |
                         Get-Mailbox |
                         Select-Object -ExpandProperty name
}

if ($corruptmailboxes -ne $null) {
    Send-MailMessage -Body ($corruptmailboxes | Out-String) -Subject "Quarantined Mailbox Report via PowerShell" -From exchange@mikefrobbins.local -To me@mikefrobbins.local -SmtpServer mail.mikefrobbins.local
}</pre>
Depending on how many mailboxes are quarantined, this method could end up being a water balloon. Water balloon? What are you talking about? If you don't know, you should watch <a href="http://mspsug.com/2013/11/19/video-from-the-november-2013-mspsug-meeting/" target="_blank">the video</a> from the November <a href="http://mspsug.com/" target="_blank">Mississippi PowerShell User Group</a> meeting.

µ