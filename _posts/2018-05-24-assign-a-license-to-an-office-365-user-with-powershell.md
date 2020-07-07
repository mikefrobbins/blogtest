---
ID: 16529
post_title: >
  Assign a License to an Office 365 User
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/05/24/assign-a-license-to-an-office-365-user-with-powershell/
published: true
post_date: 2018-05-24 07:30:20
---
There are several scenarios where you might need to assign an Office 365 license to a user. The specific scenario in this blog article is that you're migrating an Exchange Server 2010 on-premises environment to Office 365. The Exchange Server is already in hybrid mode. Users have been automatically created in Office 365 by synchronizing them from your on-premises Active Directory environment using Azure AD Connect. Users who haven't already had their mailbox moved to Office 365 will first need an Office 365 license assigned to them, and before a license can be assigned to them, a usage location must be set on their individual account.

This blog article is written using Windows 10 Enterprise Edition version 1803 and Windows PowerShell version 5.1. The examples shown in this blog article will not work with PowerShell Core. Your mileage may vary with other operating systems and other versions of PowerShell.

First, you'll need the cmdlets to perform these actions. Find the <em>MSOnline</em> module in the <a href="https://www.powershellgallery.com/" target="_blank" rel="noopener">PowerShell Gallery</a>.
<pre class="lang:ps decode:true">Find-Module -Name MSOnline</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense1a.png"><img class="alignnone size-full wp-image-16530" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense1a.png" alt="" width="859" height="131" /></a>

Install the <em>MSOnline</em> module from the PowerShell Gallery:
<pre class="lang:ps decode:true">Find-Module -Name MSOnline | Install-Module -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense2a.png"><img class="alignnone size-full wp-image-16531" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense2a.png" alt="" width="859" height="137" /></a>

Store your Office 365 credentials with sufficient access to perform these tasks in a variable.
<pre class="lang:ps decode:true">$O365Cred = Get-Credential
</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense3a.png"><img class="alignnone size-full wp-image-16533" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense3a.png" alt="" width="859" height="374" /></a>

Connect to your Office 365 account. This is the part that will generate an error if you're using PowerShell Core.
<pre class="lang:ps decode:true">Connect-MsolService -Credential $O365Cred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense4a.png"><img class="alignnone size-full wp-image-16534" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense4a.png" alt="" width="859" height="63" /></a>

Check to see if you have more than one Office 365 subscription.
<pre class="lang:ps decode:true">Get-MsolAccountSku</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense5a.png"><img class="alignnone size-full wp-image-16535" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense5a.png" alt="" width="859" height="141" /></a>

Store the specific account SKU with the licenses for the Office 365 subscription to assign to users in a variable.
<pre class="lang:ps decode:true">$LicenseSKU = Get-MsolAccountSku |
              Out-GridView -Title 'Select a license plan to assign to users' -OutputMode Single |
              Select-Object -ExpandProperty AccountSkuId</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense6a.png"><img class="alignnone size-full wp-image-16536" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense6a.png" alt="" width="859" height="310" /></a>

Find the users to assign licenses to and store them in a variable. I found it useful to narrow these results down by filtering left with the <em>UserPrincipalName</em> and/or <em>Department</em> parameters of <em>Get-MsolUser</em>.
<pre class="lang:ps decode:true">$Users = Get-MsolUser -All -UnlicensedUsersOnly |
Out-GridView -Title 'Select users to assign license plan to' -OutputMode Multiple</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense7a.png"><img class="alignnone size-full wp-image-16538" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense7a.png" alt="" width="859" height="289" /></a>

As you can see in the previous image, John Doe does not currently have a license assigned.

Assign a usage location.
<pre class="lang:ps decode:true">$Users | Set-MsolUser -UsageLocation US</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense9a.png"><img class="alignnone size-full wp-image-16540" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense9a.png" alt="" width="859" height="56" /></a>

Assign an Office 365 license.
<pre class="lang:ps decode:true">$Users | Set-MsolUserLicense -AddLicenses $LicenseSKU</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense8a.png"><img class="alignnone size-full wp-image-16539" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense8a.png" alt="" width="859" height="61" /></a>

A license has now been assigned to John Doe.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense11a.png"><img class="alignnone size-full wp-image-16545" src="http://mikefrobbins.com/wp-content/uploads/2018/05/o365-assignlicense11a.png" alt="" width="859" height="130" /></a>

Although a single user was assigned a license, with the exception of the previous command, the code as it is written in this blog article can be used to assigned licenses to multiple users.

µ