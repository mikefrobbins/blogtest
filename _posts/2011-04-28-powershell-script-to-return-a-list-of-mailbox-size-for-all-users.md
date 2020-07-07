---
ID: 2147
post_title: >
  PowerShell Script to Return a List of
  Mailbox Size for All Users
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/04/28/powershell-script-to-return-a-list-of-mailbox-size-for-all-users/
published: true
post_date: 2011-04-28 07:30:13
---
On several occasions, I've been asked to provide a report of mailbox sizes and number of items in a mailbox to a few of my customers who are running either Exchange Server 2007 or 2010. Like most of my blogs, this blog is as much documentation for myself as anything else. That way when I need to provide another one of these reports again in a few months, I'm not trying to figure out how I previously accomplished the task.

The following PowerShell Script creates a text file that contains a list of each users mailbox size for a specific mailbox database. Change the -database parameter to match the mailbox name you want to query.
<pre class="lang:ps decode:true">Get-MailboxStatistics -database "Mailbox Database 1234567890" | Sort-Object TotalItemSize -Descending | ft DisplayName, TotalItemSize, ItemCount, StorageLimitStatus &gt; c:\mailbox_size_040811.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size.png"><img class="alignnone size-full wp-image-2150" title="mb-size" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size.png" alt="" width="640" height="18" /></a>

Open the text file with Excel if additional formatting is needed.

<span style="text-decoration: underline;">08/06/2014</span>
Posting an update to this blog article since I have recently received a comment about it and this particular blog article is more than three years old.

The following results are from an Exchange 2013 Server and as you can see, the results do indeed include mailbox sizes and the number of items in each <span style="color: #000000;"><b>unique</b></span> mailbox (per user) in the specified mailbox database:
<pre class="lang:ps decode:true">Get-MailboxStatistics -Database 'Mailbox Database 1982645657' |
Sort-Object -Property TotalItemSize -Descending |
Format-Table -Property DisplayName, TotalItemSize, ItemCount, StorageLimitStatus -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size1.png"><img class="alignnone size-full wp-image-10279" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size1.png" alt="mb-size1" width="877" height="347" /></a>

As far as the original command goes, if you want the output in a text file and to look just as it does in the PowerShell console, here's the command I would use today:
<pre class="lang:ps decode:true">Get-MailboxStatistics -Database 'Mailbox Database 1982645657' |
Sort-Object -Property TotalItemSize -Descending |
Format-Table -Property DisplayName, TotalItemSize, ItemCount, StorageLimitStatus -AutoSize |
Out-File -FilePath C:\mailbox_size_080614.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size2a.png"><img class="alignnone size-full wp-image-10282" src="http://mikefrobbins.com/wp-content/uploads/2011/04/mb-size2a.png" alt="mb-size2a" width="877" height="108" /></a>

µ