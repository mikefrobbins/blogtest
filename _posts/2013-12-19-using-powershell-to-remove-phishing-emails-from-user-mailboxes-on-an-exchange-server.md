---
ID: 8752
post_title: >
  Using PowerShell to Remove Phishing
  Emails from User Mailboxes on an
  Exchange Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/12/19/using-powershell-to-remove-phishing-emails-from-user-mailboxes-on-an-exchange-server/
published: true
post_date: 2013-12-19 07:30:40
---
You've all seen those phishing emails that occasionally get past your spam filters and you all also know that no matter how many times you tell users not to open those suspicious emails and click on links contained in them, users are ultimately gullible so sometimes you have to take matters into your own hands and protect them from themselves.

Here's an example of a recent phishing email that I've seen:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email1.png"><img class="alignnone size-full wp-image-8753" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email1.png" alt="removing-email1" width="783" height="505" /></a>

My question to a user who receives this email: "Did you recently purchase something from the Apple Web Store with an American Express card?" Most likely the answer is no and most of them probably don't even have an American Express card so why would they click on the links contained in this email? You can also easily see that the URL contained in the email doesn't go to the American Express website by hovering over it.

<span style="color: #ff0000;"><em>Disclaimer: All data and information provided on this site is for informational purposes only. Mike F Robbins (mikefrobbins.com) makes no representations as to accuracy, completeness, currentness, suitability, or validity of any information on this site and will not be liable for any errors, omissions, or delays in this information or any losses, injuries, or damages arising from its display or use. All information is provided on an as-is basis.</em></span>

The examples shown in this blog article are being performed on an On-Premises Exchange 2010 server that is running PowerShell version 2.

Add the Exchange 2010 PSSnapin so the Exchange PowerShell cmdlets are available:
<pre class="wrap:false lang:ps decode:true">Add-PSSnapin -Name Microsoft.Exchange.Management.PowerShell.E2010</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email11.png"><img class="alignnone size-full wp-image-8768" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email11.png" alt="removing-email11" width="748" height="55" /></a>

In this example, I'll check to see if that particular email exists in John Doe's mailbox:
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -Identity jdoe | Search-Mailbox -SearchQuery 'Subject:"*Account Alert: Card Not Present Transaction*"' -EstimateResultOnly</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email2.png"><img class="alignnone size-full wp-image-8755" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email2.png" alt="removing-email2" width="748" height="221" /></a>

You can check all the mailboxes in your organization just as easily, although consider the performance consequences of what you're doing especially if you have thousands of mailboxes or more since it could take a considerable amount of time to complete:
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -ResultSize Unlimited |
Search-Mailbox -SearchQuery 'Subject:"*Account Alert: Card Not Present Transaction*"' -EstimateResultOnly |
Where-Object {$_.ResultItemsCount}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email3.png"><img class="alignnone size-full wp-image-8756" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email3.png" alt="removing-email3" width="748" height="340" /></a>

In this example, I'll delete that message from all mailboxes. It only existed in my mailbox and John Doe's. Note: this does not look in the user's PST files, only in their actual mailbox:
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -ResultSize Unlimited |
Search-Mailbox -SearchQuery 'Subject:"*Account Alert: Card Not Present Transaction*"' -DeleteContent |
Where-Object {$_.ResultItemsCount}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email4.png"><img class="alignnone size-full wp-image-8757" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email4.png" alt="removing-email4" width="748" height="401" /></a>

Blindly deleting those emails may not be the best approach though since if what you're matching on is incorrect, you could end up with undesirable results to say the least. It could even end up being a RGE.

The better approach is to backup the emails using a one-liner similar to the following example and then delete them so it's a lot easier to recover them if needed:
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -Identity jdoe |
Search-Mailbox -SearchQuery 'Subject:"*Account Alert: Card Not Present Transaction*"' -TargetMailbox mrobbins -TargetFolder Backup</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email5.png"><img class="alignnone size-full wp-image-8758" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email5.png" alt="removing-email5" width="748" height="233" /></a>

It's also possible to combine backing up the emails and deleting them in one step:
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -Identity jdoe |
Search-Mailbox -SearchQuery 'Subject:"*Account Alert: Card Not Present Transaction*"' -TargetMailbox 'Mike Robbins' -TargetFolder Backup -DeleteContent</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email6.png"><img class="alignnone size-full wp-image-8759" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email6.png" alt="removing-email6" width="748" height="292" /></a>

You may also want to consider backing up the emails you plan to delete to an offline PST file before deleting them because if you're deleting from all mailboxes, the backups you're placing in a mailbox may also be deleted.
<pre class="wrap:false lang:ps decode:true">Get-Mailbox -Identity 'Mike Robbins' |
New-MailboxExportRequest -ContentFilter {Subject -like "*Account Alert: Card Not Present Transaction*"} -FilePath \\mrex1\d$\tmp\mikefrobbins.pst</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email7.png"><img class="alignnone size-full wp-image-8779" src="http://mikefrobbins.com/wp-content/uploads/2013/12/removing-email7.png" alt="removing-email7" width="748" height="148" /></a>

A user at one of the customer locations that I support made the comment: "You've got more power than the NSA" when I used a similar PowerShell command to remove some phishing emails from their mailboxes. Their emails automagically came up missing :-)

µ