---
ID: 4215
post_title: 'Using the Quest Active Directory PowerShell Snapin to Search For &#038; Set Attributes'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/22/using-the-quest-active-directory-powershell-snapin-to-search-for-and-set-attributes/
published: true
post_date: 2012-05-22 07:30:47
---
I want to make sure that all users in a specific OU in my mikefrobbins.com Active Directory domain have the "Deny this user permissions to log on to Remote Desktop Session Host server" option set (checked):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp1.png"><img class="alignnone size-full wp-image-4216" title="deny-rdp1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp1.png" width="424" height="531" /></a>

Download the <a href="http://www.quest.com/powershell/activeroles-server.aspx" target="_blank">Quest Active Directory PowerShell Snapin</a> (free). The PowerShell command shown below searches this specific OU in my Active Directory domain for users where this attribute is not equal to false. The default setting is blank (allowed) as shown with the Gill Bates user below. Once this setting has been checked (set to false) and then unchecked, it has a value of true instead of being blank as shown with the John Doe user below.
<pre class="lang:ps decode:true">Add-PSSnapin Quest.ActiveRoles.ADManagement -ErrorAction SilentlyContinue
Get-QADUser * -OrganizationalUnit "ou=users,ou=test,dc=mikefrobbins,dc=com" |
?{$_.TsAllowLogon -ne $false} |
select Company, Name, Type, TsAllowLogon |
sort Company, Name |
ft -auto</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp2.png"><img class="alignnone size-full wp-image-4217" title="deny-rdp2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp2.png" width="640" height="189" /></a>

The following PowerShell command sets this value to false for all users in this OU who are not already set to false (where the value is blank or true):
<pre class="lang:ps decode:true">Add-PSSnapin Quest.ActiveRoles.ADManagement -ErrorAction SilentlyContinue
Get-QADUser * -OrganizationalUnit "ou=users,ou=test,dc=mikefrobbins,dc=com" |
?{$_.TsAllowLogon -ne $false} |
Set-QADUser -TsAllowLogon $false</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp3.png"><img class="alignnone size-full wp-image-4218" title="deny-rdp3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/deny-rdp3.png" width="640" height="139" /></a>

This does seem a little confusing because the GUI property says deny and checking it sets the value shown in PowerShell to false. In PowerShell, the property is named TSAllowedLogon so that helps clear up the confusion though.

µ