---
ID: 6955
post_title: 'PowerShell Web Access Error: &#8220;Sign-in failed. Verify that you have entered your credentials correctly.&#8221;'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/03/18/powershell-web-access-error-sign-in-failed-verify-that-you-have-entered-your-credentials-correctly/
published: true
post_date: 2013-03-18 07:30:15
---
Scenario: You've partnered with a company who is testing a new software application that uses PowerShell Web Access. You've provided this company with an Active Directory account named "Joe Doe". This account is only allowed to sign into the PowerShell Web Access interface of a server named WWW in the mikefrobbins.com domain. Password policies in this domain use the default settings.

Forty-two days after the account was created, you receive an email from the partner company asking if something has been reconfigured because they are unable to sign into PowerShell Web Access. They are receiving the error "<span style="color:#ff0000;"><em>Sign-in failed. Verify that you have entered your credentials correctly.</em></span>" when attempting to log in:

<a style="line-height:1.5;" href="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired1.png"><img class="alignnone size-full wp-image-6956" alt="pswa-pwexpired1" src="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired1.png" width="640" height="487" /></a>

Upon checking their Active Directory account, you discover that the password for the "John Doe" account is expired:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired2.png"><img class="alignnone size-full wp-image-6957" alt="pswa-pwexpired2" src="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired2.png" width="640" height="161" /></a>

Since you've only granted this user access to sign into PowerShell Web Access, they have no mechanism to change their password once it's expired, and although against security best practices, you decide to set the password for their account to never expire:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired3.png"><img class="alignnone size-full wp-image-6958" alt="pswa-pwexpired3" src="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired3.png" width="640" height="201" /></a>

John Doe is now able to log into PowerShell Web Access without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired4.png"><img class="alignnone size-full wp-image-6959" alt="pswa-pwexpired4" src="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired4.png" width="640" height="187" /></a>

One thing to note is that any account related issue such as incorrectly entering your password, the account being locked out, or the account being expired will all generate this same generic error message when trying to log into PowerShell Web Access:

<span style="line-height:1.5;">“</span><span style="color:#ff0000;"><em>Sign-in failed. Verify that you have entered your credentials correctly.</em></span><span style="line-height:1.5;">“</span>

So how do you determine what the actual cause of the error is? There are a number of different ways, but one of the scripts that I wrote for the <a href="http://manning.com/hicks/" target="_blank">PowerShell Deep Dives</a> book can simplify the process of determining what the cause of the problem is in this scenario and any scenario where an Active Directory account is experiencing logon attempt failures regardless if it's an IIS server, Exchange server, SQL server, SharePoint server, etc. This script is named "<em>Get-LogonFailures.ps1"</em> and by running it against the PowerShell Web Access server in this scenario we can see exactly why they're unable to logon. I went through a few different scenarios so you could see failures due to the users account being expired, entering their password incorrectly, and their password being expired. The following image displays a subset of the columns returned by the script:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired5a.png"><img class="alignnone size-full wp-image-6965" alt="pswa-pwexpired5a" src="http://mikefrobbins.com/wp-content/uploads/2013/03/pswa-pwexpired5a.png" width="640" height="488" /></a>

Interested in obtaining the <em>Get-LogonFailures.ps1</em> script? It's Listing #1 in Chapter 6 of the <a href="http://manning.com/hicks/" target="_blank">PowerShell Deep Dives</a> book which is currently available via the Manning Early Access Program (MEAP).

<span style="line-height:1.5;">µ</span>