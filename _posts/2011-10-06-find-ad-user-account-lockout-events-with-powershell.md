---
ID: 2934
post_title: >
  Find AD User Account Lockout Events with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/10/06/find-ad-user-account-lockout-events-with-powershell/
published: true
post_date: 2011-10-06 07:30:33
---
A few weeks ago a user contacted me and stated they were constantly being locked out throughout the day. This could have been caused by a number of things from someone else trying to log in as them to being logged in somewhere else, changing their password and the session with the old password still being active. I ran a search of the security event log on the domain controllers and found the name of the machine that the user was being locked out from. The event ID for lockout events is 4740 for Vista / 2008 and higher and 644 for 2000 / XP / 2003. Here’s the PowerShell script I used to find the lockout events:
<pre class="lang:ps decode:true">$logName = "security"
$pcName = "dc01", "dc02", "dc03"
$eventID = "4740"
Get-EventLog -LogName $logName -ComputerName $pcName | where {$_.eventID -eq $eventID} `
| fl -Property timegenerated, replacementstrings, message</pre>
Based on these results, the user is being locked out from a machine named "PC01":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-1.png"><img class="alignnone size-full wp-image-2948" title="lockout-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-1.png" width="512" height="202" /></a>

The problem was that the user recently changed their password and had some out of date credentials saved in the Windows 7 Credential Manager:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-2.png"><img class="alignnone size-full wp-image-2949" title="lockout-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-2.png" width="564" height="397" /></a>

This cmdlet will search Active Directory and list all of the accounts that are locked out:
<pre class="lang:ps decode:true">Search-ADAccount -LockedOut</pre>
Here's the results of that command:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-3.png"><img class="alignnone size-full wp-image-2950" title="lockout-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/10/lockout-3.png" width="602" height="186" /></a>

You can use the following PowerShell command to unlock the Active Directory account:
<pre class="lang:ps decode:true">$name = mike
Unlock-ADAccount $name</pre>
µ