---
ID: 16812
post_title: >
  Determine if a Mailbox is On-Premises or
  in Office 365 with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/08/02/determine-if-a-mailbox-is-on-premises-or-in-office-365-with-powershell/
published: true
post_date: 2018-08-02 07:30:38
---
One of the companies that I support is currently in the process of migrating from an on-premises Exchange Server environment to Office 365. They're currently running in hybrid mode. While it seems like wanting to know what mailboxes still exist onsite versus which ones are in the cloud would be an all too common task, there doesn't seem to be an easy way to get that information with PowerShell. You would think that you'd be able to run a PowerShell command and it would return the results. Well, not unless I'm missing something. There's no such command, or at least not one that's straight forward.
<blockquote>Note: This blog article is written using Windows 10 version 1803 and Windows PowerShell version 5.1. Your mileage may vary with different operating systems and/or different versions of PowerShell.</blockquote>
Follow the first few steps in <a href="https://mikefrobbins.com/2018/05/24/assign-a-license-to-an-office-365-user-with-powershell/" target="_blank" rel="noopener">this previous blog article</a> to install the <em>MSOnline</em> PowerShell module and connect to your Office 365 subscription. The <em>Get-MsolUser</em> cmdlet which is part of the <em>MSOnline</em> PowerShell module will be used in this blog article.

At first, it doesn't appear that <em>Get-MsolUser</em> returns any usable results for this type of information, but it does return a <em>MSExchRecipientTypeDetails</em> property which is a numeric value that can be translated into the mailbox's location. One thing that's disappointing about this cmdlet is there are almost no filters for it. Want unlicensed users, no problem, use the <em>-UnlicensedUsersOnly</em> parameter. Want the opposite condition, we'll you're out of luck with the exception of retrieving all of the users and piping them to <em>Where-Object</em>. Not real efficient when you have thousands of users and the majority of them have yet to be licensed. Luckily, for these users, a usage location hasn't been assigned and won't be until they're ready to be licensed. All of them reside in the United States so filtering using the <em>-UsageLocation</em> parameter is possible. Others may not be so lucky.
<pre class="lang:ps decode:true">Get-MsolUser -UsageLocation US |
Where-Object isLicensed -eq $true |
Select-Object -Property DisplayName, UserPrincipalName, isLicensed,
                        @{label='MailboxLocation';expression={                        
                            switch ($_.MSExchRecipientTypeDetails) {
                                      1 {'Onprem'; break}
                                      2147483648 {'Office365'; break}            
                                      default {'Unknown'}
                                  }
                        }}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/recipientdetails1a.png"><img class="alignnone size-full wp-image-16837" src="https://mikefrobbins.com/wp-content/uploads/2018/08/recipientdetails1a.png" alt="" width="860" height="605" /></a>

The previous command is written so that any users with a usage location of "US" that have a license will be returned in the results. The filter can easily be changed to meet your own needs. For example, to show unlicensed users and all of those will definitely be on-premises.

Another option is to use the <em>Get-Recipient</em> function that's part of the Exchange online PowerShell cmdlets. More information about those cmdlets can be found in <a href="https://mikefrobbins.com/2018/05/17/connect-to-office-365-with-powershell/" target="_blank" rel="noopener">this blog article</a>. <em>Get-Recipient</em> seems to have better options for filtering, but at the expense of being less accurate. It doesn't seem to show mailboxes that only exist in Office 365 (ones that weren't migrated) and things like the Discovery mailbox would need to be filtered out. Mailboxes that have been migrated have a <em>RecipientType</em> of "<em>UserMailbox</em>" and the ones that still exist on-premises have a <em>RecipientType</em> of "<em>MailboxUser</em>".
<pre class="lang:ps decode:true">Get-Recipient -RecipientType UserMailbox, MailUser -ResultSize 25</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/08/recipientdetails2a.png"><img class="alignnone size-full wp-image-16838" src="https://mikefrobbins.com/wp-content/uploads/2018/08/recipientdetails2a.png" alt="" width="859" height="439" /></a>

If you know of an easier way to accomplish this task, please post it as a comment to this blog article.

µ