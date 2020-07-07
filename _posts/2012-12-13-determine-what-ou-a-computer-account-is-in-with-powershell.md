---
ID: 5665
post_title: >
  Determine what OU a Computer Account is
  in with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/12/13/determine-what-ou-a-computer-account-is-in-with-powershell/
published: true
post_date: 2012-12-13 07:30:23
---
Problem:
You've been asked to send an email to someone with the location in Active Directory (what Organization Unit or OU) where a particular computer account is located.

Solution:
As you could have probably guessed, the solution is to query Active Directory with PowerShell and use the <em>Send-MailMessage</em> cmdlet to send the results to the recipient:
<pre class="lang:ps decode:true">Send-MailMessage -Body ((Get-ADComputer pc01).DistinguishedName |
 Out-String) -SmtpServer mail.mikefrobbins.com -From me@mikefrobbins.com -Subject 'OU Location of PC01' -To you@mikefrobbins.com</pre>
Update:
I thought I would elaborate a little more based on Jeff Hicks comments. Unless this PowerShell command is being run on a domain controller, the Remote Server Administration Tools would need to be loaded on the machine that this command is being run from. That's how you get the Microsoft Active Directory PowerShell cmdlets. If you were running PowerShell version 2, you would also have to import the ActiveDirectory PowerShell module before using those cmdlets. Here's an updated version of the same command that uses a better property which gives the location of a computer account in Active Directory from what I'll call the top down view instead of the bottom up:
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Send-MailMessage -Body ((Get-ADComputer pc01 -Properties CanonicalName).CanonicalName |
 Out-String) -SmtpServer mail.mikefrobbins.com -From me@mikefrobbins.com -Subject 'OU Location of PC01' -To you@mikefrobbins.com</pre>
If you don't know who <a href="http://twitter.com/JeffHicks" target="_blank">Jeff Hicks</a> is, you should definitely check out <a href="http://jdhitsolutions.com/blog/" target="_blank">his blog</a>. It's an honor to have someone like him critique my scripts. I consider Jeff a friend and a mentor. You should also check out <a href="http://www.trainsignal.com/blog/powershell-challenge-jeff-hicks" target="_blank">his PowerShell Challenge</a> on <a href="http://www.trainsignal.com/blog/" target="_blank">TrainSignal's blog</a> this week.

µ