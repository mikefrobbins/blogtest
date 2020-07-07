---
ID: 1227
post_title: Mailbox Move Failure on Exchange 2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/09/02/mailbox-move-failure-exchange-2010/
published: true
post_date: 2010-09-02 05:30:39
---
You’ve attempted to move mailboxes to your new Exchange 2010 server and you receive the following error:
<em> Active Directory operation failed on dc1.domain.name. This error is not retriable. Additional information: Insufficient access rights to perform the operation. Active directory response: 00002098: SecErr: DSID-03150BB9, problem 4003 (INSUFF_ACCESS_RIGHTS), data 0 The user has insufficient access rights. Exchange Management Shell command attempted: ‘dc1.domain.name/users/test user’ | New-MoveRequest-TargetDatabase ‘Mailbox Database 1’</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob1.jpg"><img class="alignnone size-medium wp-image-1229" title="moveprob1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob1.jpg?w=300" width="300" height="262" /></a>

Subsequent attempts to move the mailbox result in the following error:
<em> The queue in ‘Mailbox Database 1’ database already contains a move request for ‘Test User’, while AD reports the mailbox as not being moved. It is possible that someone created this move request recently, while targeting a different domain controller and AD replication did not yet occur. You can examine this move request by running ‘Get-MoveRequestStatistics –MoveRequestQueue ‘Mailbox Database 1’ –MailboxGuid 123456 –IncludeReport | fl’. If you believe this to be an abandoned move request, you can remove it by running ‘Remove-MoveRequest –MoveRequestQueue ‘Mailbox Database –MailboxGuid 123456’.</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob2.jpg"><img class="alignnone size-medium wp-image-1230" title="moveprob2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob2.jpg?w=300" width="300" height="264" /></a>

The initial problem is due to permissions being removed from the users Active Directory account. Open “Active Directory Users and Computers”. Select “Advanced Features” under “View” at the top:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob3.jpg"><img class="alignnone size-medium wp-image-1231" title="moveprob3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob3.jpg?w=300" width="300" height="202" /></a>

Locate the users Active Directory account, right click it, select properties, select the security tab, and click the “Advanced” button to display the screen below. Check the “Include inheritable permissions from this object’s parent” check box and click “OK”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob41.jpg"><img class="alignnone size-medium wp-image-1232" title="moveprob4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/moveprob41.jpg?w=300" width="300" height="227" /></a>

At this point the initial issue is resolved, but the move request still exists even though they cannot be seen in the GUI. Open “Exchange Management Shell” on the Exchange 2010 server. Using the users GUID and Mailbox Database information from the second attempt at moving the mailbox run the following PowerShell commands. Run this command to verify there is a move status on the account in question:
<pre class="lang:ps decode:true">Get-MoveRequestStatistics -MoveRequestQueue 'Mailbox Database 1' -MailboxGuid 123456 -IncludeReport | fl</pre>
Run this command to forcibly remove the move status:
<pre class="lang:ps decode:true">Remove-MoveRequest -MoveRequestQueue 'Mailbox Database 1' -MailboxGuid 123456</pre>
Trying to move the mailbox after adding the above permissions and removing the prior move status should now allow you to successfully move the mailbox or mailboxes that previously failed.

µ