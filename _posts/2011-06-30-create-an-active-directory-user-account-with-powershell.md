---
ID: 2443
post_title: >
  Create an Active Directory User Account
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/06/30/create-an-active-directory-user-account-with-powershell/
published: true
post_date: 2011-06-30 07:30:02
---
I’m in the process of installing SQL Denali and need a couple of users accounts created.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts1.png"><img class="alignnone size-full wp-image-2451" title="denali-service-accts1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts1.png" width="640" height="480" /></a>

If you are creating the Active Directory user on a machine other than a domain controller, you’ll need to install the Active Directory module for Windows PowerShell. Then import the Active Directory module.
<pre class="lang:ps decode:true">Import-Module ServerManager
 Add-WindowsFeature RSAT-AD-PowerShell
 Import-Module ActiveDirectory</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts2.png"><img class="alignnone size-full wp-image-2452" title="denali-service-accts2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts2.png" width="634" height="149" /></a>

To see the syntax and available options for creating an Active Directory user using PowerShell, type "Get-Help New-ADUser" inside the PowerShell console.

Store the password in a variable. Using the -assecurestring parameter masks the password. Once you have the statement formulated that you want to run, give it a try using the -WhatIf parameter to see what the results will be if you execute the statement. If the results look acceptable, remove the -WhatIf parameter and run it for real this time.
<pre class="lang:ps decode:true">$Password = Read-Host -assecurestring "Account Password"
New-ADUser -Name "sqlDenaliAgent" -AccountPassword $Password -Description "SQL Server Agent Account for Denali Test Server" -Enabled $true -PasswordNeverExpires $true -Path "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com" -SamAccountName "sqlDenaliAgent" -UserPrincipalName "sqlDenaliAgent@mikefrobbins.com"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts3.png"><img class="alignnone size-full wp-image-2453" title="denali-service-accts3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts3.png" width="640" height="63" /></a>

One of the snags I ran into is the default users OU has to be specified with a CN=Users, but any of the OU's you've created and some of the default ones use OU=<em>Name</em>. A coworker of mine that I worked with about 10 years ago said the difference is whether or not it has a "ham sandwich" in the folder picture next to the OU as shown in the image below:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts4.png"><img class="alignnone size-full wp-image-2454" title="denali-service-accts4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/denali-service-accts4.png" width="300" height="406" /></a>

µ