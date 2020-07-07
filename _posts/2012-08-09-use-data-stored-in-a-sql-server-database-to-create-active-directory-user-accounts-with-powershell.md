---
ID: 5104
post_title: >
  Use Data Stored in a SQL Server Database
  to Create Active Directory User Accounts
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/08/09/use-data-stored-in-a-sql-server-database-to-create-active-directory-user-accounts-with-powershell/
published: true
post_date: 2012-08-09 07:30:23
---
I need a few Active Directory users created in my mikefrobbins.com test environment so I thought why come up with fake information when I could use information that I already have in a SQL Server database? The Employees table in the Northwind database looks like an easy enough candidate since all the data I need is in one table. This is about the concept and not about seeing how complicated I can make this process. Here's the type of information I'll pull out of this database to use for the Active Directory user accounts:
<pre class="lang:ps decode:true">Import-Module SQLPS
Invoke-Sqlcmd -ServerInstance sql01 -Database NORTHWIND -Query `
 "select top 1 FirstName, LastName, Extension, Title, Address, Region,
 City, PostalCode, HomePhone from Employees"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers1.jpg"><img class="alignnone size-full wp-image-5105" title="northwind-adusers1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers1.jpg" width="533" height="251" /></a>

Here's the PowerShell script to create the AD Users based on what's in the Employee table in the Northwind SQL Server database. I'm running SQL Server 2012 which uses a PowerShell module. The same thing can be accomplished with SQL Server 2008 R2, but it uses a PowerShell Snap-in.
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Import-Module SQLPS
Invoke-Sqlcmd -ServerInstance sql01 -Database NORTHWIND -Query `
 "select FirstName, LastName, Extension, Title, Address, Region,
 City, PostalCode, HomePhone from Employees" |
select @{l='Name';e={$_.firstname+" "+$_.lastname}},
 @{l='SamAccountName';e={$_.firstname.tolower().substring(0,1)+$_.lastname.tolower()}},
 @{l='UserPrincipalName';e={$_.firstname.tolower().substring(0,1)+$_.lastname.tolower(
 )+"@mikefrobbins.com"}},
 @{l='DisplayName';e={$_.firstname+" "+$_.lastname}},
 @{l='GivenName';e={$_.firstname}},
 @{l='Surname';e={$_.lastname}},
 @{l='OfficePhone';e={$_.extension}},
 @{l='StreetAddress';e={$_.address}},
 @{l='State';e={$_.region}},
 title, homephone, city, postalcode |
New-ADUser -Path "OU=NorthwindUsers,DC=mikefrobbins,DC=com" -PassThru |
select Name, SamAccountName, UserPrincipalName | ft -auto</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers2.jpg"><img class="alignnone size-full wp-image-5106" title="northwind-adusers2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers2.jpg" width="640" height="450" /></a>

Let's validate the first user listed above in Active Directory:
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Get-ADUser -Identity 'ndavolio' | fl *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers31.jpg"><img class="alignnone size-full wp-image-5108" title="northwind-adusers31" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers31.jpg" width="562" height="227" /></a>

The <em>Get-ADUser</em> PowerShell cmdlet is what I call "pre-filtered". You can see in the previous screenshot that the property count is 10, so what happened to all the other properties I set? In order to see all of the properties, you have to use the -properties parameter and specify an "*":
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Get-ADUser -Identity 'ndavolio' -Properties *</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers4.jpg"><img class="alignnone size-full wp-image-5109" title="northwind-adusers4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers4.jpg" width="640" height="483" /></a>

As you can see there are a ton of properties. Let's select only the ones we set when creating the user account:
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Get-ADUser -Identity 'ndavolio' -Properties Name, SamAccountName, UserPrincipalName, `
 DisplayName, GivenName, Surname, OfficePhone, StreetAddress, State, title, `
 homephone, city, postalcode</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers5.jpg"><img class="alignnone size-full wp-image-5110" title="northwind-adusers5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers5.jpg" width="624" height="352" /></a>

Now anytime I need some test Active Directory users created, I can create the Northwind ones in less than a second:
<pre class="lang:ps decode:true">Measure-Command {
Import-Module ActiveDirectory
Import-Module SQLPS
Invoke-Sqlcmd -ServerInstance sql01 -Database NORTHWIND -Query `
 "select FirstName, LastName, Extension, Title, Address, Region,
 City, PostalCode, HomePhone from Employees" |
select @{l='Name';e={$_.firstname+" "+$_.lastname}},
 @{l='SamAccountName';e={$_.firstname.tolower().substring(0,1)+$_.lastname.tolower()}},
 @{l='UserPrincipalName';e={$_.firstname.tolower().substring(0,1)+$_.lastname.tolower(
 )+"@mikefrobbins.com"}},
 @{l='DisplayName';e={$_.firstname+" "+$_.lastname}},
 @{l='GivenName';e={$_.firstname}},
 @{l='Surname';e={$_.lastname}},
 @{l='OfficePhone';e={$_.extension}},
 @{l='StreetAddress';e={$_.address}},
 @{l='State';e={$_.region}},
 title, homephone, city, postalcode |
New-ADUser -Path "OU=NorthwindUsers,DC=mikefrobbins,DC=com"
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers61.jpg"><img class="alignnone size-full wp-image-5112" title="northwind-adusers61" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/08/northwind-adusers61.jpg" width="640" height="466" /></a>

µ