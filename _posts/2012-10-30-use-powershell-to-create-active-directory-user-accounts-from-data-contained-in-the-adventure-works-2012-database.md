---
ID: 5637
post_title: >
  Use PowerShell to Create Active
  Directory User Accounts from Data
  Contained in the Adventure Works 2012
  Database
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/30/use-powershell-to-create-active-directory-user-accounts-from-data-contained-in-the-adventure-works-2012-database/
published: true
post_date: 2012-10-30 07:30:53
---
This past Saturday, I presented a session at <a href="http://powershellsaturday.com/003/" target="_blank">PowerShell Saturday 003</a> in Atlanta. Towards the end of the presentation, I created 290 Active Directory user accounts by using the information for employees contained in the Adventure Works 2012 database. This is actually a PowerShell script that I whipped up Friday night at the hotel after the speaker dinner. I populated some demographic information by joining multiple tables together from that particular database. There is more demographic information to be had, but I got tired of joining tables and thought this was good enough for a proof of concept.

I'm using implicit remoting to keep from having to install the Active Directory tools or SQL tools on the Windows 8 computer that this script is being run from.
<pre class="lang:ps decode:true">Import-PSSession -Session (New-PSSession -ComputerName dc01) -Module ActiveDirectory
Import-PSSession -Session (New-PSSession -ComputerName sql01) -Module SQLPS

Invoke-Sqlcmd -ServerInstance sql01 -Database AdventureWorks2012 -Query `
"select Employee.LoginID, Person.FirstName as givenname, Person.LastName as surname,
 Employee.JobTitle as title, Address.AddressLine1 as streetaddress, Address.City,
 Address.PostalCode, PersonPhone.PhoneNumber as officephone
from HumanResources.Employee
 join Person.Person
 on Employee.BusinessEntityID = Person.BusinessEntityID
 join Person.PersonPhone
 on Person.BusinessEntityID = PersonPhone.BusinessEntityID
 join Person.BusinessEntityAddress
 on PersonPhone.BusinessEntityID = BusinessEntityAddress.BusinessEntityID
 join Person.Address
 on BusinessEntityAddress.AddressID = Address.AddressID" |

select @{l='Name';e={$_.givenname+" "+$_.surname}},
 @{l='SamAccountName';e={$_.loginid.tolower().substring(16)}},
 @{l='UserPrincipalName';e={$_.loginid.tolower().substring(16)+"@mikefrobbins.com"}},
 @{l='DisplayName';e={$_.givenname+" "+$_.surname}},
 title, givenname, surname, officephone, streetaddress, postalcode, city |

New-ADUser -Path "OU=AdventureWorks Users,OU=Users,OU=Test,DC=mikefrobbins,DC=com" -PassThru |
select Name, SamAccountName, UserPrincipalName | Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users1.png"><img class="alignnone size-full wp-image-5638" title="create-aw-users1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users1.png" width="640" height="531" /></a>

The amazing thing is the entire process including creating the PSSessions only takes 23 seconds to create all 290 Active Directory user accounts:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users2.png"><img class="alignnone size-full wp-image-5643" title="create-aw-users2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users2.png" width="640" height="520" /></a>

If the PSSessions are created first, it only takes 5 seconds to create all 290 user accounts:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users3.png"><img class="alignnone size-full wp-image-5644" title="create-aw-users3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/create-aw-users3.png" width="640" height="444" /></a>

Except for creating the PSSessions, this is all a PowerShell one liner! The crazy insane thing is these VM's are running on an older single CPU system with an E6600 CPU and 8GB of RAM, although all of the servers are running server core with no GUI (low overhead :-)).

For anyone looking for the slide deck from my session, it can be downloaded <a href="http://powershellsaturday.com/003/presentation/powershell-fundamentals-for-beginners/" target="_blank">here</a>.

µ