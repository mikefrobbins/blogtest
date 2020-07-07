---
ID: 8719
post_title: >
  Setting an Active Directory User Account
  to Expire at a Specific Time of Day with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/12/12/setting-an-active-directory-user-account-to-expire-at-a-specific-time-of-day-with-powershell/
published: true
post_date: 2013-12-12 07:30:28
---
Notice that in Active Directory Users and Computers (ADUC) when setting the expiration of a user account, there's only a way to have the account expire at the end of a specific day:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration1.png"><img class="alignnone size-full wp-image-8720" alt="ad-expiration1" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration1.png" width="414" height="498" /></a>

The same option exists in the Active Directory Administrative Center (ADAC):

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration2.png"><img class="alignnone size-full wp-image-8721" alt="ad-expiration2" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration2.png" width="802" height="384" /></a>

In ADAC, you can see the PowerShell command that the GUI uses to accomplish this task:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration3.png"><img class="alignnone size-full wp-image-8722" alt="ad-expiration3" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration3.png" width="675" height="522" /></a>

Let's query that particular property with PowerShell to see exactly what it's now set to:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties AccountExpirationDate |
Select-Object -Property SamAccountName, AccountExpirationDate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration4.png"><img class="alignnone size-full wp-image-8723" alt="ad-expiration4" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration4.png" width="617" height="161" /></a>

Notice in the previous results, that there's not only a date, but a time as well.

Using PowerShell, I'll set the AccountExpirationDate to the specific date and time when I want the account to expire:
<pre class="lang:ps decode:true">Set-ADAccountExpiration -Identity alan0 -DateTime '12/10/2013 17:00:00'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration5.png"><img class="alignnone size-full wp-image-8724" alt="ad-expiration5" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration5.png" width="684" height="68" /></a>

Now I'll double check the value of what that particular property is set to again:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties AccountExpirationDate |
Select-Object -Property SamAccountName, AccountExpirationDate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration6.png"><img class="alignnone size-full wp-image-8725" alt="ad-expiration6" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration6.png" width="613" height="160" /></a>

One thing I noticed is that once the date and time set for the account to expire was reached, the user was prevented from logging into a pc, but it took a while before they were prevented from logging into Outlook Web Access. Just something to keep in mind :-)

What if you change your mind after setting this value and want to set it so the account doesn't expire? Since I originally set this property using the GUI I don't know what the default value was. I'll take a look at another account to see what it's set to:
<pre class="lang:ps decode:true">Get-ADUser -Identity jason0 -Properties AccountExpirationDate |
Select-Object -Property SamAccountName, AccountExpirationDate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration7.png"><img class="alignnone size-full wp-image-8740" alt="ad-expiration7" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration7.png" width="619" height="159" /></a>

So it needs to be set to nothing. I'll try setting it to $null to see if that works:
<pre class="lang:ps decode:true">Set-ADAccountExpiration -Identity alan0 -DateTime $null</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration8.png"><img class="alignnone size-full wp-image-8741" alt="ad-expiration8" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration8.png" width="569" height="65" /></a>

Looks like that worked:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties AccountExpirationDate |
Select-Object -Property SamAccountName, AccountExpirationDate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration9.png"><img class="alignnone size-full wp-image-8742" alt="ad-expiration9" src="http://mikefrobbins.com/wp-content/uploads/2013/12/ad-expiration9.png" width="613" height="158" /></a>

<em>Note: The examples shown in this blog article require the Remote Server Administration Tools (RSAT) to be installed on the workstation these commands are being run from (specifically, the Active Directory PowerShell module). The workstation these examples were run from has PowerShell version 4 installed so the module auto-loading feature that was introduced in PowerShell version 3 loaded the Active Directory module and there was no need to explicitly import the Active Directory PowerShell module.</em>

µ

&nbsp;