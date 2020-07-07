---
ID: 8936
post_title: >
  Add an Active Directory User to the Same
  Groups as Another User with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/01/30/add-an-active-directory-user-to-the-same-groups-as-another-user-with-powershell/
published: true
post_date: 2014-01-30 07:30:06
---
A request has been received to grant additional permissions to an existing user in your organizations Active Directory environment. The username of this existing user is "frank0". In additional to his current responsibilities, Frank will be taking on the responsibilities of Alan who goes by the username of "alan0".

<em>Note: The examples shown in this blog article are being performed on a Windows 8.1 machine that has the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=39296" target="_blank">remote server administration tools</a> installed. The Active Directory module is not explicitly imported in these examples since Windows 8.1 runs PowerShell version 4 and the module auto-loading feature which was first introduced in PowerShell version 3 takes care of importing the module.</em>

First, take a look at what Active Directory groups "alan0" is a member of. These are the groups that "frank0" needs to be made a member of:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties memberof |
Select-Object -ExpandProperty memberof</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup1a.png"><img class="alignnone size-full wp-image-8940" alt="ad-copygroup1a" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup1a.png" width="600" height="145" /></a>

The dotted notation style of accessing the MemberOf property could also be used:
<pre class="lang:ps decode:true">(Get-ADUser -Identity alan0 -Properties memberof).memberof</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup2.png"><img class="alignnone size-full wp-image-8938" alt="ad-copygroup2" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup2.png" width="600" height="125" /></a>

Frank is currently a member of the "Information Technology" group:
<pre class="lang:ps decode:true">(Get-ADUser -Identity frank0 -Properties memberof).memberof</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup3a.png"><img class="alignnone size-full wp-image-8944" alt="ad-copygroup3a" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup3a.png" width="600" height="76" /></a>

A simple one-liner can be used to add Frank as a member of each of Alan's groups:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties memberof |
Select-Object -ExpandProperty memberof |
Add-ADGroupMember -Members frank0</pre>
Nothing is returned by default if the command completes successfully:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup4.png"><img class="alignnone size-full wp-image-8942" alt="ad-copygroup4" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup4.png" width="600" height="97" /></a>

Use the <em>-PassThru</em> parameter with the previous command to receive feedback about what groups Frank is being added as a member of:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties memberof |
Select-Object -ExpandProperty memberof |
Add-ADGroupMember -Members frank0 -PassThru | 
Select-Object -Property SamAccountName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup6.png"><img class="alignnone size-full wp-image-8948" alt="ad-copygroup6" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup6.png" width="600" height="230" /></a>

In addition to the "Information Technology" group, Frank is now a member of all the groups that Alan is a member of:
<pre class="lang:ps decode:true">(Get-ADUser -Identity frank0 -Properties memberof).memberof</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup5a.png"><img class="alignnone size-full wp-image-8945" alt="ad-copygroup5a" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup5a.png" width="600" height="134" /></a>

Want to add multiple users to the same groups that Alan is a member of? No problem:
<pre class="lang:ps decode:true">Get-ADUser -Identity alan0 -Properties memberof |
Select-Object -ExpandProperty memberof |
Add-ADGroupMember -Members frank0, gary0, jack0, john0, michael0, paul0</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup7.png"><img class="alignnone size-full wp-image-8950" alt="ad-copygroup7" src="http://mikefrobbins.com/wp-content/uploads/2014/01/ad-copygroup7.png" width="600" height="97" /></a>

µ