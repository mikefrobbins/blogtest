---
ID: 2464
post_title: 'Microsoft SharePoint Foundation 2010 Installation &#8211; Part 1'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/07/07/microsoft-sharepoint-foundation-2010-installation-part-1/
published: true
post_date: 2011-07-07 07:30:56
---
You're ready to introduce SharePoint 2010 into your <a href="http://en.wikipedia.org/wiki/Small_and_medium_businesses" target="_blank">SMB</a> and you've chosen to install the "<a href="http://sharepoint.microsoft.com/en-us/buy/Pages/Licensing-Details.aspx" target="_blank">free with Windows Server</a>" version called "Foundation". Details about this version can be found on the SharePoint Foundation 2010 <a href="http://sharepoint.microsoft.com/en-us/product/Related-Technologies/Pages/SharePoint-Foundation.aspx" target="_blank">Production Information</a> webpage. You'll need a minimum of two servers, virtual or physical with the specifications listed below to get started. While it is possible to run SharePoint 2010 on a single server, it's definitely not recommended for a production environment. Depending on the service applications you choose to enable and the number of users who will be concurrently using SharePoint, additional servers may be required. Some organizations will choose to install on a minimum of three servers for redundancy of the Web Front End.

<span style="text-decoration: underline;">Dedicated SQL Server</span>:
Install Microsoft SQL Server 2008 R2 x64 on a dedicated database server. SQL 2008 (non-R2 and SQL 2005 are supported with specific updates installed, but SQL 2008 R2 and SharePoint 2010 are considered to work “<a href="http://technet.microsoft.com/en-us/library/cc990273.aspx" target="_blank">Better Together</a>” according to a Whitepaper available on TechNet. SQL 2008 R2 will run on multiple operating systems, but I prefer to run it on Windows Server 2008 R2. Depending on the size and utilization of your SharePoint farm, other databases may reside on the SQL Server, but it should at least be a dedicated database server that's only used for SQL Server databases. The SQL Server should be assigned a minimum of 4 cores. Here's a good <a href="http://msdn.microsoft.com/en-us/library/ms143506.aspx" target="_blank">MSDN article</a> on the hardware and software requirements for installing SQL Server 2008 R2.

<span style="text-decoration: underline;">SharePoint 2010 Application / Web Front End Server</span>:
A base installation of Windows Server 2008 R2 should be performed. The operating system should be assigned a minimum of 8 GB of RAM for a production environment. The system drive should be a minimum of 80 GB. The SharePoint server should be assigned a minimum of 4 cores. Here's a good <a href="http://technet.microsoft.com/en-us/library/cc262485.aspx" target="_blank">TechNet Article</a> on the hardware and software requirements for SharePoint 2010.

All of the normal tasks per your organization's standards should be performed on these servers before attempting to install SharePoint. This includes assigning static IP addresses, adding them to the domain, running Windows Updates until no further updates are available, installing Antivirus, Backup Client, setting up a Backup Job, Monitoring, etc.

While not a requirement for SharePoint 2010, I'm installing the PowerShell ISE and enabling scripts on my SharePoint server since it's one of the main tools I'll be using:
<pre class="lang:ps decode:true">Import-Module ServerManager
 Add-WindowsFeature PowerShell-ISE
 Set-ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install11.png"><img class="alignnone size-full wp-image-2476" title="sp2010-install1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install11.png" width="640" height="148" /></a>

Launch the PowerShell ISE:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install2.png"><img class="alignnone size-full wp-image-2471" title="sp2010-install2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install2.png" width="211" height="307" /></a>

Install and Import the Active Directory Module for Windows PowerShell:
<pre class="lang:ps decode:true">Import-Module ServerManager
Add-WindowsFeature RSAT-AD-PowerShell
Import-Module ActiveDirectory</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install3.png"><img class="alignnone size-full wp-image-2472" title="sp2010-install3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install3.png" width="589" height="269" /></a>

Create 3 service accounts in Active Directory (sp<em>Name</em>Farm, sp<em>Name</em>Install, and sp<em>Name</em>Pool). This is the minimum number of service accounts required just to install SharePoint. You may end up needing more service accounts. Todd Klindt has an <a href="http://www.toddklindt.com/blog/Lists/Posts/Post.aspx?ID=237" target="_blank">excellent blog</a> on service accounts.
<pre class="lang:ps decode:true">$Password = Read-Host -assecurestring "SP Farm Account Password"
$Name = "spExtranetFarm"
$UPN = "spExtranetFarm@mikefrobbins.com"
$Description = "SharePoint Farm Administrator Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN
$Password = Read-Host -assecurestring "SP Install Account Password"
$Name = "spExtranetInstall"
$UPN = "spExtranetInstall@mikefrobbins.com"
$Description = "SharePoint Installation Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN
$Password = Read-Host -assecurestring "SP AppPool Account Password"
$Name = "spExtranetPool"
$UPN = "spExtranetPool@mikefrobbins.com"
$Description = "SharePoint Application Pool Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install41.png"><img class="alignnone size-full wp-image-2480" title="sp2010-install4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install41.png" width="611" height="499" /></a>

Add the sp<em>Name</em>Farm and sp<em>Name</em>Install accounts to the local administrators group on the SharePoint server:
<pre class="lang:ps decode:true">$User = [ADSI]("WinNT://mikefrobbins/spExtranetFarm")
$Group = [ADSI]("WinNT://sharepoint/Administrators")
$Group.PSBase.Invoke("Add",$User.PSBase.Path)
$User = [ADSI]("WinNT://mikefrobbins/spExtranetInstall")
$Group = [ADSI]("WinNT://sharepoint/Administrators")
$Group.PSBase.Invoke("Add",$User.PSBase.Path)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install5.png"><img class="alignnone size-full wp-image-2487" title="sp2010-install5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install5.png" width="460" height="238" /></a>

Add the sp<em>Name</em>Install account to the SQL Server dbcreator and securityadmin roles:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install6.png"><img class="alignnone size-full wp-image-2489" title="sp2010-install6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install6.png" width="318" height="268" /></a>

Test connectivity from the SharePoint server to the SQL server. One easy way to do this is to telnet from the SharePoint server to port 1433 on the SQL server. You’ll need to add the Telnet client to the SharePoint server:
<pre class="lang:ps decode:true">Import-Module ServerManager
Add-WindowsFeature Telnet-Client</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install7.png"><img class="alignnone size-full wp-image-2491" title="sp2010-install7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install7.png" width="369" height="223" /></a>

This shows a failure to connect to the SQL server:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install8.png"><img class="alignnone size-full wp-image-2493" title="sp2010-install8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install8.png" width="640" height="93" /></a>

I’m running SQL Denali CTP1 on this particular SQL server. TCP/IP was disabled under SQL Server Network Configuration in the SQL Server Configuration Manager:
<span class="Apple-style-span" style="font-size: 15px; line-height: 22px; white-space: pre;"><a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install9.png"><img class="alignnone size-full wp-image-2495" title="sp2010-install9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install9.png" width="442" height="189" /></a></span>

Change TCP/IP to enabled and restart the SQL Server services:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install10.png"><img class="alignnone size-full wp-image-2496" title="sp2010-install10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install10.png" width="640" height="290" /></a>

SQL was also not allowed through the firewall on the SQL Server. I added an exception:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install111.png"><img class="alignnone size-full wp-image-2497" title="sp2010-install11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install111.png" width="589" height="391" /></a>

A successful telnet connection from the SharePoint server to port 1433 on the SQL Server:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install121.png"><img class="alignnone size-full wp-image-2499" title="sp2010-install12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install121.png" width="189" height="43" /></a>

We now have all of the preliminary work out of the way. Login to the SharePoint server as the sp<em>Name</em>Install account. Download <a href="http://technet.microsoft.com/en-us/sharepoint/ee263910" target="_blank">SharePoint Foundation 2010</a> if you have not done so already. <a href="http://mikefrobbins.com/2011/07/14/microsoft-sharepoint-foundation-2010-installation-part-2/" target="_blank">Part 2</a> of this blog article series, which will guide you through the actual installation process, will be posted on Thursday July 14th.

µ<span class="Apple-style-span" style="font-size: 15px; line-height: 22px; white-space: pre;">
</span>