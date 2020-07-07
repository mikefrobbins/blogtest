---
ID: 3251
post_title: >
  Unable to Grant Domain Local Groups Full
  Access Permission to a Exchange 2010
  Mailbox using the GUI
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/01/26/unable-to-grant-domain-local-group-full-access-permission-to-an-exchange-2010-mailbox-using-the-gui/
published: true
post_date: 2012-01-26 07:30:09
---
John Doe is a user in your Active Directory environment (Windows Server 2008 R2 Forest Function Level) with a mailbox on the email server (Exchange Server 2010 with SP2):
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui1.png"><img class="alignnone size-full wp-image-3254" title="dl-gui1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui1.png" width="424" height="549" /></a>

You want to grant a domain local group named "Test Group" the full access permission to John Doe's mailbox:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui2.png"><img class="alignnone size-full wp-image-3255" title="dl-gui2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui2.png" width="404" height="448" /></a>

You attempt to grant this permission by selecting "Manage Full Access Permission" from the Exchange 2010 Management Console:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui31.png"><img class="alignnone size-full wp-image-3257" title="dl-gui31" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui31.png" width="637" height="556" /></a>

When you click add and search for the group, it doesn't appear:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui4.png"><img class="alignnone size-full wp-image-3258" title="dl-gui4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui4.png" width="506" height="460" /></a>

PowerShell to the Rescue! The only way I've figured out how to accomplish this (work-around for this issue) is to use the  "Exchange Management Shell" (PowerShell). In this scenario, use the following PowerShell script:
<pre class="lang:ps decode:true">Add-MailboxPermission -Identity "John Doe" -User "Test Group" -AccessRights "FullAccess"</pre>
Even though the syntax uses the parameter name "-User", you can specify a group name.
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui5.png"><img class="alignnone size-full wp-image-3259" title="dl-gui5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui5.png" width="640" height="105" /></a>

The full access permission for John Doe's mailbox is now assigned to the "Test Group":
<a href="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui6.png"><img class="alignnone size-full wp-image-3260" title="dl-gui6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/01/dl-gui6.png" width="637" height="556" /></a>

µ