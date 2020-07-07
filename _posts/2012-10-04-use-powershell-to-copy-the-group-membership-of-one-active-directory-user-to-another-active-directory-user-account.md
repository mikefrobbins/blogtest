---
ID: 5517
post_title: >
  Use PowerShell to Copy the Group
  Membership of one Active Directory User
  to Another Active Directory User Account
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/04/use-powershell-to-copy-the-group-membership-of-one-active-directory-user-to-another-active-directory-user-account/
published: true
post_date: 2012-10-04 07:30:04
---
You have an Active Directory user account and you want to make a second user a member of the same groups without removing the second user from any groups they may already be a member of.

I prefer using the <a href="http://www.quest.com/powershell/activeroles-server.aspx" target="_blank">Quest PowerShell Cmdlets for Active Directory</a> for doing my AD administration work. They have been downloaded and installed on the system this is being run from. The Quest snap-in has been added to make the cmdlets available.

User 'afuller' is a member of several groups in this active directory environment and the user 'lcallahan' is currently only a member of the domain users group as shown below:
<pre class="lang:ps decode:true">Add-PSSnapin -Name Quest.ActiveRoles.ADManagement
Get-QADUser 'afuller' |
Get-QADMemberOf

Get-QADUser 'lcallahan' |
Get-QADMemberOf</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups1.png"><img class="alignnone size-full wp-image-5518" title="user-copy-adgroups1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups1.png" width="614" height="404" /></a>

I want 'lcallahan' to be a member of the same groups as 'afuller'. I attempt a one liner which generates an error because 'lcallahan' can't be added to the domain users group since that user is already a member of it. Strangely enough, already being a member of the domain users group seems to be the only group that causes this error. If the user is a member of any of the other groups already, those other groups don't cause any errors.
<pre class="lang:ps decode:true">Get-QADUser 'afuller' |
Get-QADMemberOf |
Add-QADGroupMember -Member 'lcallahan'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups2.png"><img class="alignnone size-full wp-image-5519" title="user-copy-adgroups2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups2.png" width="613" height="212" /></a>

I'll exclude that group by using the <em>Where-Object</em> cmdlet and since PowerShell verison 3 is also installed on the system this is being run from, I'll use the new simplified syntax for <em>Where-Object</em>.
<pre class="lang:ps decode:true">Get-QADUser 'afuller' |
Get-QADMemberOf |
where name -ne 'domain users' |
Add-QADGroupMember -Member 'lcallahan'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups3.png"><img class="alignnone size-full wp-image-5520" title="user-copy-adgroups3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups3.png" width="614" height="291" /></a>

Now 'lcallahan' is a member of the same groups as 'afuller'. This wouldn't affect any of the groups that 'lcallahan' was already a member of.
<pre class="lang:ps decode:true">Get-QADUser 'lcallahan' |
Get-QADMemberOf</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups4.png"><img class="alignnone size-full wp-image-5521" title="user-copy-adgroups4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/user-copy-adgroups4.png" width="611" height="277" /></a>

<span style="text-decoration: underline;">Update 02/11/14</span>
I've written an updated version of this blog article that uses the Microsoft Active Directory PowerShell cmdlets that are part of the Remote Server Administration Tools (RSAT): <a href="http://mikefrobbins.com/2014/01/30/add-an-active-directory-user-to-the-same-groups-as-another-user-with-powershell/" target="_blank">http://mikefrobbins.com/2014/01/30/add-an-active-directory-user-to-the-same-groups-as-another-user-with-powershell/</a>

µ