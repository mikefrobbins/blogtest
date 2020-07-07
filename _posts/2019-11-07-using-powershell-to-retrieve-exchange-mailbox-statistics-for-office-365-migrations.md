---
ID: 18151
post_title: >
  Using PowerShell to Retrieve Exchange
  Mailbox Statistics for Office 365
  Migrations
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/11/07/using-powershell-to-retrieve-exchange-mailbox-statistics-for-office-365-migrations/
published: true
post_date: 2019-11-07 07:30:26
---
Recently, I've been working on trying to finish up a migration from Exchange Server 2010 to Office 365. There are potentially numerous mailboxes that aren't used and those won't be migrated to Office 365 because there's no sense in paying for licensing for them.

How do you determine what mailboxes are in use?

First, use implicit remoting to load the Exchange cmdlets locally. Years ago, I would install the Exchange cmdlets locally, but it was brought to my attention that it's unsupported, at least according to this article: "<em><a href="https://blogs.technet.microsoft.com/rmilne/2015/01/28/directly-loading-exchange-2010-or-2013-snapin-is-not-supported/" target="_blank" rel="noopener noreferrer">Directly Loading Exchange 2010 or 2013 SnapIn Is Not Supported</a></em>".
<pre class="lang:ps decode:true ">$Cred = Get-Credential
$PSSession = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://&lt;ExchangeServerFQDN&gt;/PowerShell/ -Authentication Kerberos -Credential $Cred
Import-PSSession -Session $PSSession</pre>
Next retrieve a list of all mailboxes with <em>Get-Mailbox</em> and pipe those results to <em>Get-MailboxStatistics</em>. I've silenced any warnings, otherwise the output may be littered with warnings for users who have never logged into their mailbox. Sorting the results by <em>LastLogonTime</em> in descending order places the most recently accessed mailboxes at the top of the list. Since I'm returning more than four properties and I want them in a table, I'll pipe to <em>Format-Table</em> and specify the properties I've found to be the most useful.
<pre class="lang:ps decode:true">Get-Mailbox -ResultSize Unlimited |
Get-MailboxStatistics -WarningAction SilentlyContinue |
Sort-Object -Property LastLogonTime -Descending |
Format-Table -Property DisplayName, ItemCount, TotalItemSize, Last*</pre>
Specifying <em>Last*</em> for one of the properties returns the <em>LastLoggedOnUserAccount</em>, <em>LastLogoffTime</em>, and <em>LastLogonTime</em>. <em>LastLogoffTime</em> may not always be accurate or populated. At first, I thought the results were inaccurate due to an old account showing up with a recent <em>LastLogonTime</em>. The <em>LastLoggedOnUserAccount</em> for that account showed that someone else was accessing it which made more sense.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ