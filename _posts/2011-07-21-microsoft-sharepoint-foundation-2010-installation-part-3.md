---
ID: 2541
post_title: >
  Microsoft SharePoint Foundation 2010
  Installation – Part 3
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/07/21/microsoft-sharepoint-foundation-2010-installation-part-3/
published: true
post_date: 2011-07-21 07:30:13
---
This blog is a continuation from last weeks blog (<a href="http://mikefrobbins.com/2011/07/14/microsoft-sharepoint-foundation-2010-installation-part-2/" target="_blank">Part 2</a>). We’ll jump right in where we left off. Verify you are logged into the server that you want to install SharePoint on as the sp<em>Name</em>Install account. Right click the PowerShell ISE and select "Run as administrator":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install20.png"><img title="sp2010-install20" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install20.png" width="412" height="516" /></a>

Load the SharePoint PowerShell Snap-in:
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install21.png"><img title="sp2010-install21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install21.png" width="637" height="156" /></a>You will receive "The local farm is not accessible. Cmdlets with FeatureDependencyId are not registered." This is normal since we have not configured the farm yet.

We'll be stepping through the same process that's completed when you run the configuration wizard, except using PowerShell. This process is documented on <a href="http://technet.microsoft.com/en-us/library/ff806336.aspx" target="_blank">TechNet</a>.
<pre class="lang:ps decode:true">$FarmCredential = Get-Credential -credential mikefrobbins\spExtranetFarm
$Passphrase = Read-Host -assecurestring "SP PassPhrase"
$DbName = "SP_Extranet_Config"
$DbServer = "sql.mikefrobbins.com"
$AdminContentDb = "SP_Extranet_AdminContent"
New-SPConfigurationDatabase -DatabaseName $DbName -DatabaseServer $DbServer -AdministrationContentDatabaseName $AdminContentDb -FarmCredentials $FarmCredential -Passphrase $Passphrase
Install-SPHelpCollection -All
Initialize-SPResourceSecurity
Install-SPService
Install-SPFeature -AllExistingFeatures
New-SPCentralAdministration -Port 45000 -WindowsAuthProvider "NTLM"
Install-SPApplicationContent</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install221.png"><img class="alignnone size-full wp-image-2547" title="sp2010-install221" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install221.png" width="640" height="208" /></a>

You'll be prompted to enter the password for the sp<em>Name</em>Farm account:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install23.png"><img class="alignnone size-full wp-image-2543" title="sp2010-install23" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install23.png" width="326" height="254" /></a>

You'll also be prompted for a passphrase which is used to add additional SharePoint servers to your farm in the future:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install24.png"><img class="alignnone size-full wp-image-2544" title="sp2010-install24" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install24.png" width="430" height="103" /></a>

It will take a few minutes for the script to complete. You'll know it's running by the run button being grayed out and the stop command being enabled. Do not interrupt the script.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install25.png"><img class="alignnone size-full wp-image-2545" title="sp2010-install25" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install25.png" width="496" height="73" /></a>

When the script finishes, you'll see output similar to this:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install251.png"><img class="alignnone size-full wp-image-2629" title="sp2010-install251" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install251.png" width="634" height="172" /></a>

If you're running SQL Denali CTP3, this initial configuration will fail since SharePoint 2010 (non-SP1) attempts to use a feature that is deprecated and has been removed in CTP3:
<em>New-SPConfigurationDatabase : Could not find stored procedure 'sp_dboption'.</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install261.png"><img class="alignnone size-full wp-image-2620" title="sp2010-install261" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install261.png" width="555" height="76" /></a>

If you receive the error above which is documented on <a href="http://msdn.microsoft.com/en-us/library/hh231665(v=sql.110).aspx" target="_blank">MSDN</a>, drop the database that was created, load <a href="http://support.microsoft.com/kb/2460058" target="_blank">SP1</a> for SharePoint Foundation 2010 before preceding any further, and then re-run the script. If you have not installed SharePoint yet, you could <a href="http://blogs.msdn.com/b/ronalg/archive/2011/07/11/slipstream-sharepoint-2010-sp1-and-language-packs-w-sp1-into-rtm.aspx" target="_blank">slipstream</a> SP1 into the SharePoint installation media and save yourself some time and trouble.

Register the sp<em>Name</em>Pool account as a SharePoint Managed Service Account:
<pre class="lang:ps decode:true">$PoolCredential = Get-Credential -credential mikefrobbins\spExtranetPool
New-SPManagedAccount –Credential $PoolCredential</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install27.png"><img class="alignnone size-full wp-image-2548" title="sp2010-install27" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install27.png" width="640" height="183" /></a>

From Internet Information Services Manager, stop the default website and set it to listen on a port other than 80, 443, and the port that is being used by the Central Admin website.

Create a Web Application and Site Collection:
<pre class="lang:ps decode:true">$WebAppPool = "SP - Extranet"
$WebAppName = "SP - Extranet"
$WebAppPoolAcct = "mikefrobbins\spExtranetPool"
$WebAppDbName = "SP_Extranet_Content"
$WebAppDbServer = "sql.mikefrobbins.com"
$WebAppPath = "D:\inetpub\wwwroot\wss\VirtualDirectories\Extranet"
$WebAppPort = "80"
$SiteColUrl = ("http://extranet.mikefrobbins.com")
$SiteColOwner = "mikefrobbins\spExtranetFarm"
$SiteColDescription = "Mike F Robbins - Computing Solutions Extranet"
$SiteColName = "Extranet"
$SiteColTemplate = "STS#0"
New-SPWebApplication -ApplicationPool $WebAppPool -Name $WebAppName -ApplicationPoolAccount (Get-SPManagedAccount $WebAppPoolAcct) `
-DatabaseName $WebAppDbName -DatabaseServer $WebAppDbServer -Path $WebAppPath -Port $WebAppPort -Url $SiteColUrl
New-SPSite -Url $SiteColUrl -OwnerAlias $SiteColOwner -Description $SiteColDescription -Name $SiteColName -Template $SiteColTemplate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install28.png"><img class="alignnone size-full wp-image-2549" title="sp2010-install28" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install28.png" width="640" height="387" /></a>

Set the appropriate DNS records. The sp<em>Name</em>Farm account is the only account that currently has access to your new SharePoint site. Welcome to your site!
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install29.png"><img class="alignnone size-full wp-image-2553" title="sp2010-install29" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install29.png" width="640" height="399" /></a>

Setup a SQL backup job for the SharePoint databases. Re-Run Windows Updates on the SharePoint server. Add additional web front end servers.

µ