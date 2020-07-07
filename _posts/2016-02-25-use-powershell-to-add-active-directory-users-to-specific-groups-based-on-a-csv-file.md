---
ID: 13444
post_title: >
  Use PowerShell to Add Active Directory
  Users to Specific Groups based on a CSV
  file
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/02/25/use-powershell-to-add-active-directory-users-to-specific-groups-based-on-a-csv-file/
published: true
post_date: 2016-02-25 07:30:21
---
I recently responded to a post in a forum about adding Active Directory users to groups with PowerShell based on information contained in a CSV (Comma Separated Values file format). I thought I would not only share the scenario and solution that I came up with but also elaborate on adding additional functionality that may be desired.

In this scenario, you’ve been provided with a CSV file that contains a list of Active Directory users and the groups that they should be a member of as shown in the following image:
<pre class="lang:ps decode:true ">Import-Csv -Path C:\tmp\UserGroups.csv | Format-Table</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/02/adusergroups1b.png" rel="attachment wp-att-13461"><img class="alignnone size-full wp-image-13461" src="http://mikefrobbins.com/wp-content/uploads/2016/02/adusergroups1b.png" alt="adusergroups1b" width="859" height="263" /></a>

Conventional wisdom would lead you down the path of iterating through each user and adding them to each group individually. How inefficient do you think that would be? In my opinion, it would be very inefficient to say the least. Also consider that this sounds like a task that will need to be repeated whenever new employees are hired or during those all too common restructuring of personnel events.

The Active Directory PowerShell module that's installed as part of the RSAT (<a href="https://www.microsoft.com/en-gb/download/details.aspx?id=45520" target="_blank">Remote Server Administration Tools</a>) has previously been installed on the Windows 10 computer that's being used throughout this blog article. It's also a member of the mikefrobbins.com Active Directory domain.

Let's create a test OU (Organizational Unit) and then both the test users and groups that are specified in the CSV file:
<pre class="lang:ps decode:true">New-ADOrganizationalUnit -Name Test -Path 'DC=mikefrobbins,DC=com'

1..12 |
ForEach-Object {
    New-ADUser -Name aduser$_ -Path 'OU=Test,DC=mikefrobbins,DC=com'
    New-ADGroup -Name adgroup$_ -GroupScope Global -Path 'OU=Test,DC=mikefrobbins,DC=com'
}</pre>
After taking a look at the <a href="https://technet.microsoft.com/en-us/%5Clibrary/hh852331(v=wps.630).aspx" target="_blank">help</a> for the <em>Add-ADGroupMember</em> cmdlet, only a single group can be specified at a time but multiple users can be added to that single group at once. Knowing this means that it would be much more efficient to iterate through the groups and add all of the necessary users to that particular group at once instead of iterating through each individual user and adding them to each specific group (one at a time):
<pre class="lang:ps decode:true">$Header = ((Get-Content -Path C:\tmp\UserGroups.csv -TotalCount 1) -split ',').Trim()
$Users = Import-Csv -Path C:\tmp\UserGroups.csv

foreach ($Group in $Header[1..($Header.Count -1)]) {
    Add-ADGroupMember -Identity $Group -Members ($Users | Where-Object $Group -eq 'X' | Select-Object -ExpandProperty $Header[0])
    Remove-ADGroupMember -Identity $Group -Members ($Users | Where-Object $Group -ne 'X' | Select-Object -ExpandProperty $Header[0]) -Confirm:$false
}</pre>
I could also derive the information retrieved and stored in the <em>Header</em> variable from the <em>Users</em> variable but there's not much overhead in querying the file twice since only the first row of the CSV is retrieved during one of those queries based on how the <em>Get-Content</em> command is written in the first line of the previous code example. Retrieving the header names (which become the property names) with a separate command has the added benefit of allowing all of the column names to be renamed in subsequent CSV's without dependencies on their names as long as the users are specified in the first column and the group names are listed in the first row beginning with the second column.

The additional functionality that I've added in this blog article but not to my response in the forum post is logic to remove users from the groups where they're not specified in the CSV as being a member of the group. This will be beneficial in those restructuring scenarios that I mentioned earlier to prevent users from having unnecessary rights when they're moved to a new job role and the old permissions are no longer necessary for them to perform the duties required by their new position.

Consider adding logic to verify that the users and groups exist because errors will be generated if they don't. If you wanted to turn this into a reusable tool that could be handed off to someone else, I would also recommend adding error handling as well as support for <em>WhatIf</em> and <em>Confirm</em>.

µ