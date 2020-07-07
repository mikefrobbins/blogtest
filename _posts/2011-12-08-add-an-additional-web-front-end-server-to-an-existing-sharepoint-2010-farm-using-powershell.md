---
ID: 3097
post_title: >
  Add an Additional Web Front-end Server
  to an Existing SharePoint 2010 Farm
  using PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/12/08/add-an-additional-web-front-end-server-to-an-existing-sharepoint-2010-farm-using-powershell/
published: true
post_date: 2011-12-08 07:30:11
---
You've followed the <a href="http://mikefrobbins.com/2011/07/07/microsoft-sharepoint-foundation-2010-installation-part-1/" target="_blank">instructions</a> in my other three blogs and built a SharePoint 2010 farm (not a stand-alone installation) with one or more web front-end servers.

Per one of the notes in a <a href="http://technet.microsoft.com/en-us/library/cc261752.aspx" target="_blank">TechNet article</a> I found:  “As a best practice, we recommend the operating system on the new server should be at the same service pack level and have the same security updates and other hotfixes as the existing farm servers.”  This article also shows the steps that I’ll be demonstrating in this blog.

All of the normal tasks per your organization’s standards should be performed on this server before attempting to install SharePoint. This includes assigning a static IP address, adding it to the domain, running Windows Updates until no further updates are available, installing Antivirus, Backup Client, setting up a Backup Job, Monitoring, etc.

While not a requirement for SharePoint 2010, I’m installing the PowerShell ISE and enabling scripts on my SharePoint server since it’s one of the main tools I’ll be using:
<pre class="lang:ps decode:true">Import-Module ServerManager
Add-WindowsFeature PowerShell-ISE
Set-ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install11.png"><img class="alignnone size-full wp-image-2476" title="sp2010-install1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install11.png" width="640" height="148" /></a>

Add the spNameFarm and spNameInstall accounts to the local administrators group on the SharePoint server:
<pre class="lang:ps decode:true">$User = [ADSI]("WinNT://mikefrobbins/spExtranetFarm")
$Group = [ADSI]("WinNT://sharepoint/Administrators")
$Group.PSBase.Invoke("Add",$User.PSBase.Path)
$User = [ADSI]("WinNT://mikefrobbins/spExtranetInstall")
$Group = [ADSI]("WinNT://sharepoint/Administrators")
$Group.PSBase.Invoke("Add",$User.PSBase.Path)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install5.png"><img class="alignnone size-full wp-image-2487" title="sp2010-install5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install5.png" width="460" height="238" /></a>

Follow my entire blog on “<a href="http://mikefrobbins.com/2011/07/14/microsoft-sharepoint-foundation-2010-installation-part-2/" target="_blank">Microsoft SharePoint Foundation 2010 Installation – Part 2</a>”.

Verify you are logged into the server that you want to install SharePoint on as the spNameInstall account. Right click the PowerShell ISE and select “Run as administrator”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install20.png"><img class="alignnone size-full wp-image-2537" title="sp2010-install20" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install20.png" width="412" height="516" /></a>

Load the SharePoint PowerShell Snap-in:
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install21.png"><img class="alignnone size-full wp-image-2538" title="sp2010-install21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install21.png" width="637" height="156" /></a>

Run the following Powershell script to add this new server as an additional web front-end server in the existing SharePoint 2010 farm. Enter the passphrase that was specified when the farm was initially created when prompted. If you’ve forgotten the passphrase, it can be reset by following my <a href="http://mikefrobbins.com/2011/12/01/reset-the-sharepoint-2010-passphrase-with-powershell/" target="_blank">blog</a> on resetting it.
<pre class="lang:ps decode:true">$DbName = "SP_Extranet_Config"
$DbServer = "sql01.mikefrobbins.com"
$Passphrase = Read-Host -assecurestring "SP PassPhrase"
Connect-SPConfigurationDatabase -DatabaseServer $DbServer -DatabaseName $DbName -Passphrase $Passphrase
Install-SPHelpCollection -All
Initialize-SPResourceSecurity
Install-SPService
Install-SPFeature -AllExistingFeatures
Install-SPApplicationContent</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/sp-addfrontend1.png"><img class="alignnone size-full wp-image-3099" title="sp-addfrontend1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/sp-addfrontend1.png" width="640" height="270" /></a>

If the command completes successfully, this server is now a web front-end server. You'll need to setup load balancing or use round-robin DNS to distribute traffic between the web front-end servers.

Remember that any manual changes to your web front-ends such as <a href="http://mikefrobbins.com/2011/09/01/resolving-sharepoint-2010-pdf-issues-with-powershell/" target="_blank">adding a PDF icon</a> must be made on each server or the end user experience will be inconsistent.

µ