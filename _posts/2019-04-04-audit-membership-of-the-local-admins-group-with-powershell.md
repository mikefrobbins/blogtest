---
ID: 17735
post_title: >
  Audit Membership of the Local Admins
  Group with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/04/04/audit-membership-of-the-local-admins-group-with-powershell/
published: true
post_date: 2019-04-04 07:30:42
---
Recently, I needed to make sure that specific accounts were members of the local administrators group on several servers along with making sure that no other users were members of it.

PowerShell version 5.1 introduced a module named <em>Microsoft.PowerShell.LocalAccounts</em> that contains the following commands for managing local users and groups.
<pre class="lang:ps decode:true">Get-Command -Module Microsoft.PowerShell.LocalAccounts</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups1a.jpg"><img class="alignnone size-full wp-image-17736" src="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups1a.jpg" alt="" width="859" height="339" /></a>

Checking the group membership is as easy as running <em>Get-LocalGroupMember</em> within the script block of <em>Invoke-Command</em> and targeting remote systems.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql14, sql16, sql17 {
    Get-LocalGroupMember -Group Administrators
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups2a.jpg"><img class="alignnone size-full wp-image-17737" src="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups2a.jpg" alt="" width="859" height="246" /></a>

Adding a user to the group is also simple. The commands seem very basic, although they get the job done. I was expecting an <em>Identity</em> parameter and maybe a <em>PassThru</em> parameter, but no such luck.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql14, sql16, sql17 {
    Add-LocalGroupMember -Group Administrators -Member mikefrobbins\mike0
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups3a.jpg"><img class="alignnone size-full wp-image-17738" src="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups3a.jpg" alt="" width="859" height="90" /></a>

You could also group your output to make it easier to determine who's on first and what's on second.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName sql14, sql16, sql17 {
    Get-LocalGroupMember -Group Administrators
} | Sort-Object -Property PSComputerName |
Format-Table -GroupBy PSComputerName

Invoke-Command -ComputerName sql14, sql16, sql17 {
    Get-LocalGroupMember -Group Administrators
} | Sort-Object -Property Name |
Format-Table -GroupBy Name</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups4a.jpg"><img class="alignnone size-full wp-image-17740" src="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups4a.jpg" alt="" width="859" height="808" /></a>

And of course, removing a user is also easy and very similar to adding a user.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName sql14, sql16, sql17 {
    Remove-LocalGroupMember -Group Administrators -Member mikefrobbins\mike0
    Get-LocalGroupMember -Group Administrators
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups5a.jpg"><img class="alignnone size-full wp-image-17741" src="https://mikefrobbins.com/wp-content/uploads/2019/04/audit-local-groups5a.jpg" alt="" width="859" height="258" /></a>

Another thought is that you could use the <em>Write-SqlTableData</em> and <em>Read-SqlTableData</em> commands that are part of <a href="https://docs.microsoft.com/en-us/sql/powershell/download-sql-server-ps-module" target="_blank" rel="noopener noreferrer">the SQLServer PowerShell module</a> to store this information in a database and compare it later to determine if any group membership changes have been made.

Please post any comments, questions, and/or suggestions as a comment to this blog article.

µ