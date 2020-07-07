---
ID: 10337
post_title: 'PowerShell: When Best Practices and Accurate Results Collide'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/11/06/powershell-when-best-practices-and-accurate-results-collide/
published: true
post_date: 2014-11-06 07:30:38
---
I'm a big believer in trying to write my PowerShell code to what the industry considers to be the best practices as most are common sense anyway, although as one person once told me: "Common sense isn't all that common anymore".

I would hope that even the most diehard best practices person would realize that if you run into a scenario where following best practices causes the results to be skewed, that at least in that scenario it's worth taking a step back so you can see the bigger picture. I recently encountered a couple of these scenarios which I'll demonstrate in this blog article.

Scenario 1: You want to find all users in the NorthWind Active Directory OU (Organizational Unit) whose department property does not have a value equal to "Sales".
<pre class="lang:ps decode:true ">Get-ADUser -Filter {Department -ne 'Sales'} -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Department |
Select-Object -Property Name, Department</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results1.png"><img class="alignnone size-full wp-image-10338" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results1.png" alt="accurate-results1" width="877" height="168" /></a>

In the previous example, I've followed the best practices by filtering left and while everything looks correct, unfortunately there is some sort of filtering going on behind the scenes that the filter parameter is performing which skews the results.

This time, I'll grab all of the users in the NorthWind OU and pipe them to the <em>Where-Object</em> cmdlet to perform the filtering:
<pre class="lang:ps decode:true ">Get-ADUser -Filter * -SearchBase 'OU=Northwind Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com' -Properties Department |
Where-Object Department -ne 'Sales' |
Select-Object -Property Name, Department</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results2.png"><img class="alignnone size-full wp-image-10339" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results2.png" alt="accurate-results2" width="877" height="203" /></a>

Notice that although you would think that both of these commands in the previous two examples would return the same results, the ones without a Department are somehow filtered out by the first command even though their department is not equal to "Sales".

Scenario 2: You want to return a list of SQL Server users from the default instance on a server named SQL01 where the password expiration policy is not enabled:
<pre class="lang:ps decode:true ">$SQL = New-Object –TypeName Microsoft.SqlServer.Management.Smo.Server -ArgumentList 'sql01'
$SQL.Logins |
Where-Object {-not $_.PasswordExpirationEnabled} |
Select-Object -Property Name, LoginType</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results3.png"><img class="alignnone size-full wp-image-10340" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results3.png" alt="accurate-results3" width="877" height="275" /></a>

Those results look plausible, but unfortunately they're inaccurate. As we can see in the following results, the PasswordExpirationEnabled property is a Boolean:
<pre class="lang:ps decode:true ">$SQL.Logins |
Get-Member -Name PasswordExpirationEnabled</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results4.png"><img class="alignnone size-full wp-image-10341" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results4.png" alt="accurate-results4" width="877" height="192" /></a>

Conventional wisdom would lead you to believe that the valid values for a Boolean are true or false but it's that assumption that if something isn't true, it must be false that leads to inaccurate results in this scenario.

By adding the PasswordExpirationEnabled property to our results, we can see where the logic problem occurs:
<pre class="lang:ps decode:true ">$SQL.Logins |
Where-Object {-not $_.PasswordExpirationEnabled} |
Select-Object -Property Name, LoginType, PasswordExpirationEnabled</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results5.png"><img class="alignnone size-full wp-image-10342" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results5.png" alt="accurate-results5" width="877" height="263" /></a>

Based on the previous results, you can see that the value can also be null. The problem can also be seen in the GUI since it's not possible to set the PasswordExpirationEnabled option in SQL for a Windows user:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results7.png"><img class="alignnone size-full wp-image-10344" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results7.png" alt="accurate-results7" width="704" height="632" /></a>

To get accurate results, we'll need to see if the value is false because not true could be false or null:
<pre class="lang:ps decode:true ">$SQL.Logins |
Where-Object {$_.PasswordExpirationEnabled -eq $false} |
Select-Object -Property Name, LoginType, PasswordExpirationEnabled</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results6.png"><img class="alignnone size-full wp-image-10343" src="http://mikefrobbins.com/wp-content/uploads/2014/08/accurate-results6.png" alt="accurate-results6" width="877" height="192" /></a>

As you can see, those results are accurate based on the value of the PasswordExpirationEnabled property.

µ