---
ID: 10253
post_title: >
  Set a Users Active Directory Display
  Name with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/08/07/set-a-users-active-directory-display-name-with-powershell/
published: true
post_date: 2014-08-07 07:30:23
---
I recently saw an article on how to set a users Active Directory display name based on the values of their given name, initials, and surname. I came up with my own unique solution for this task and thought I would share it with you, the readers of my blog.

As you can see in the following example, there are a mixture of users who need their display name corrected based on the requirement that their display name be listed as "Givenname Initials Surname":
<pre class="lang:ps decode:true ">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties DisplayName, Initials |
Select-Object -Property DisplayName, GivenName, Initials, Surname</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname1.png"><img class="alignnone size-full wp-image-10254" src="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname1.png" alt="displayname1" width="877" height="263" /></a>

I use a simple one-liner to correct the display name of all of the users in the Northwind Active Directory OU (Organizational Unit). One issue though, is that not all users have a middle initial and those users will end up with two spaces in their display name between their first and last name. Instead of using logic to correct that issue, I decided to use a regular expression that matches one or more spaces that are followed by a space:
<pre class="lang:ps decode:true ">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Initials |
ForEach-Object {
    Set-ADUser -Identity $_ -DisplayName ("$($_.GivenName) $($_.Initials) $($_.Surname)" -replace '\s+(?=\s)')
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname2.png"><img class="alignnone size-full wp-image-10255" src="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname2.png" alt="displayname2" width="877" height="119" /></a>

The previous command is indeed a PowerShell one-liner even though it is on more than one physical line and formatted for readability. A PowerShell one-liner is a command that is one continuous pipeline regardless of how many physical lines it is on. A PowerShell one-liner is NOT defined by the lack of carriage returns &lt;period&gt;.
<pre class="lang:ps decode:true ">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties DisplayName, Initials |
Select-Object -Property DisplayName, GivenName, Initials, Surname</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname3.png"><img class="alignnone size-full wp-image-10256" src="http://mikefrobbins.com/wp-content/uploads/2014/07/displayname3.png" alt="displayname3" width="877" height="263" /></a>

As you can see in the previous set of results, this task is complete and although only nine users were updated in this example, it could have just as easily been 900 users.

If you live within driving distance of the Birmingham Alabama area, Id like to invite you to attend <a href="http://www.sqlsaturday.com/328/eventhome.aspx" target="_blank">SQL Saturday #328</a> on Saturday, August 23rd. I'll be presenting two awesome PowerShell sessions during the all day track that's dedicated to PowerShell (room 327).

µ