---
ID: 16554
post_title: 'Configuring &#8220;Send As&#8221; Permission in Office 365 using PowerShell'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/05/31/configuring-send-as-permission-in-office-365-using-powershell/
published: true
post_date: 2018-05-31 07:30:41
---
You're the administrator of an on-premises Exchange Server 2010 environment that's in Hybrid mode. After migrating a few users to Office 365, you start receiving complaints that they're no longer able to send emails as their departments group.

First, follow the instructions in one of my previous blog articles to "<a href="http://mikefrobbins.com/2018/05/17/connect-to-office-365-with-powershell/" target="_blank" rel="noopener">Connect to Office 365 using PowerShell</a>".

The following command grants John Doe the ability to send as the Facility Services group in Office 365.
<pre class="lang:ps decode:true ">Add-RecipientPermission -Identity facilityservices@mspsug.com -AccessRights SendAs -Trustee jdoe@mspsug.com -Con
firm:$false</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/office365-sendas1a.png"><img class="alignnone size-full wp-image-16555" src="http://mikefrobbins.com/wp-content/uploads/2018/05/office365-sendas1a.png" alt="" width="859" height="159" /></a>

It's also possible to grant the user the ability to send on behalf of the group. More information about granting Office 365 users the ability to "Send As" and "Send on Behalf" of Office 365 groups can be found in the Microsoft support article "<a href="https://support.office.com/en-us/article/allow-members-to-send-as-or-send-on-behalf-of-an-office-365-group-admin-help-0ad41414-0cc6-4b97-90fb-06bec7bcf590" target="_blank" rel="noopener">Allow members to send as or send on behalf of an Office 365 Group - Admin help</a>".

µ