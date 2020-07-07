---
ID: 189
post_title: >
  Creating a Shared Mailbox in Exchange
  2007
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/28/creating-a-shared-mailbox-in-exchange-2007/
published: true
post_date: 2009-09-28 20:55:54
---
Open Exchange Management Shell and execute the New-Mailbox cmdlet using the following example as a template:

<img class="alignnone size-full wp-image-191" title="ps_shared_mailbox" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ps_shared_mailbox.jpg" alt="ps_shared_mailbox" width="600" height="171" />
<pre class="lang:ps decode:true ">New-Mailbox -Alias MySharedMailbox -Name 'My Shared Mailbox' -Database 'MAIL1\First Storage Group\Mailbox Database' -OrganizationalUnit 'mikefrobbins.demo/My OU/Users/Mailbox' -Shared -UserPrincipalName mysharedmailbox@mikefrobbins.demo</pre>
The following cmdlet assigns full access for the mailbox to a group in Active Directory named “My Shared Mailbox Admins” which needs to exist in AD prior to executing this command:

<img class="alignnone size-full wp-image-190" title="ps_mailbox_rights" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ps_mailbox_rights.jpg" alt="ps_mailbox_rights" width="600" height="138" />
<pre class="lang:ps decode:true">Get-Mailbox -Identity 'My Shared Mailbox' |
Add-MailboxPermission -User 'My Shared Mailbox Admins' -AccessRights FullAccess</pre>
This cmdlet allows members of the “My Shared Mailbox Admins” group in Active Directory to be able to send email from the "mysharedmailbox@mikefrobbins.demo" email account:

<img class="alignnone size-full wp-image-192" title="ps_mailbox_sendas" src="http://mikefrobbins.com/wp-content/uploads/2009/09/ps_mailbox_sendas.jpg" alt="ps_mailbox_sendas" width="600" height="171" />
<pre class="lang:ps decode:true">Get-Mailbox -Identity 'My Shared Mailbox' |
Add-ADPermission -User 'My Shared Mailbox Admins' -AccessRights: ReadProperty, WriteProperty -Properties 'Personal Information' -ExtendedRights Send-As</pre>
Once these steps are completed, any AD user who is added to the "My Shared Mailbox Admins" group in AD will be able to open the "mysharedmailbox" and send email from this account.

µ