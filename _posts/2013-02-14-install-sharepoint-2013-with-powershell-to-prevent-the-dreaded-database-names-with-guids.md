---
ID: 6674
post_title: 'Install SharePoint 2013 with PowerShell to Prevent the Dreaded Database Names with GUID&#8217;s'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/02/14/install-sharepoint-2013-with-powershell-to-prevent-the-dreaded-database-names-with-guids/
published: true
post_date: 2013-02-14 07:30:03
---
Do you have a shared SQL Server back-end for multiple SharePoint farms? Do you like being able to easily tell which SharePoint databases go with which SharePoint farm? If you install SharePoint 2013 using the GUI wizard, you’ll end up with database names that have GUID’s on them that look like this:

<img class="alignnone size-full wp-image-6675" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-1.png" alt="sp2013-1" width="404" height="38" />

This blog will guide you through installing SharePoint Foundation 2013 with PowerShell so you’ll have full control over the database names. This process is very similar to installing SharePoint Foundation 2010 which I've <a href="http://mikefrobbins.com/2011/07/07/microsoft-sharepoint-foundation-2010-installation-part-1/" target="_blank">previously blogged about</a> in more detail.

While it is possible to install SharePoint on a single server, it's definitely not recommended for a production environment. The process that I'm going through in this blog uses what I consider to be the bare minimum configuration (in my opinion) which is one server dedicated to SQL Server that can be a shared database server but doesn't perform any other roles in your data-center, the second server is a dedicated SharePoint web front-end server.

First, <a href="http://www.microsoft.com/en-us/download/details.aspx?id=35488" target="_blank">download SharePoint Foundation 2013</a>, make sure the servers you plan to install SharePoint on meet the <a href="http://technet.microsoft.com/en-us/library/cc262485.aspx" target="_blank">hardware and software requirements</a> and read the installation guide:

<img class="alignnone size-full wp-image-6676" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-2.png" alt="sp2013-2" width="375" height="385" />

Create three service accounts. A farm account, an install account, and an application pool account (this is the minimum number of service accounts). I'm running this PowerShell script from a Windows 8 workstation that's a member of the mikefrobbins.com domain. I'm using implicit remoting so I don't have to install the Remote Server Administration Tools on this workstation since it's a temporary test environment and I'm only importing a single cmdlet (<em>New-ADUser</em>) to maintain maximum security since that's the only cmdlet in the Active Directory module that this script uses. I've also specified the<em> -Credential</em> parameter since the user I'm running PowerShell as doesn't have rights to create an Active Directory user account.
<pre class="lang:ps decode:true">Import-PSSession -Session (
 New-PSSession -ComputerName dc01 -Credential (Get-Credential)
) -CommandName New-ADUser

$Password = Read-Host -assecurestring "SP2013 Farm Account Password"
$Name = "spExtranetFarm"
$UPN = "spExtranetFarm@mikefrobbins.com"
$Description = "SharePoint Farm Administrator Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN

$Password = Read-Host -assecurestring "SP2013 Install Account Password"
$Name = "spExtranetInstall"
$UPN = "spExtranetInstall@mikefrobbins.com"
$Description = "SharePoint Installation Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN

$Password = Read-Host -assecurestring "SP2013 AppPool Account Password"
$Name = "spExtranetPool"
$UPN = "spExtranetPool@mikefrobbins.com"
$Description = "SharePoint Application Pool Account - Extranet"
$Path = "ou=service,ou=accounts,ou=test,dc=mikefrobbins,dc=com"
New-ADUser -Name $Name -AccountPassword $Password -Description $Description `
-Enabled $true -PasswordNeverExpires $true -Path $Path -SamAccountName $Name `
-UserPrincipalName $UPN</pre>
<img class="alignnone size-full wp-image-6677" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-3.png" alt="sp2013-3" width="573" height="536" />

Add the Farm and Install accounts to the local administrators group on the server that will become the SharePoint web front-end server. In this example, I'm also running this script from the same Windows 8 workstation as the previous script and I'm taking advantage of the PowerShell remoting <em>Invoke-Command</em> cmdlet since I've previously enabled PowerShell remoting on the server named SP2013 that will become the SharePoint web front-end server. In this scenario, the server named SP2013 is running Windows Server 2008 R2 with Service Pack 1. Once again, I've specified the <em style="color: #444444;">-Credential</em> parameter since the user account I'm logged in to the workstation as and running PowerShell as is a user who has almost no rights. This allows me to specify credentials with elevated permissions that only this script will run as to maintain maximum security of this environment.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sp2013 {
 $User = [ADSI]("WinNT://mikefrobbins/spExtranetFarm")
 $Group = [ADSI]("WinNT://sp2013/Administrators")
 $Group.PSBase.Invoke("Add",$User.PSBase.Path)
 $User = [ADSI]("WinNT://mikefrobbins/spExtranetInstall")
 $Group = [ADSI]("WinNT://sp2013/Administrators")
 $Group.PSBase.Invoke("Add",$User.PSBase.Path)
} -Credential (Get-Credential)</pre>
<img class="alignnone size-full wp-image-6679" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-4.png" alt="sp2013-4" width="440" height="158" />

Add the Install account to the dbcreator and securityadmin roles on the SQL Server. I don't have the SQL Management Studio Tools installed on the workstation this PowerShell script is being run from either so I'm going to use implicit remoting as with the prior example where I imported the <em>New-ADUser</em> cmdlet except this time the PSSession will be to the SQL Server and I'll import the<em> Invoke-Sqlcmd</em> cmdlet. Both the domain controller and SQL Server in this environment are running Windows Server 2012 so PowerShell remoting is enabled by default on both of them.
<pre class="lang:ps decode:true">Import-PSSession -Session (
 New-PSSession -ComputerName sql01 -Credential (Get-Credential)
) -CommandName Invoke-Sqlcmd

Invoke-Sqlcmd -ServerInstance sql01 -Database master –Query `
"USE [master]
GO
CREATE LOGIN [MIKEFROBBINS\spExtranetInstall] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
GO
ALTER SERVER ROLE [dbcreator] ADD MEMBER [MIKEFROBBINS\spExtranetInstall]
GO
ALTER SERVER ROLE [securityadmin] ADD MEMBER [MIKEFROBBINS\spExtranetInstall]
GO"</pre>
<img class="alignnone size-full wp-image-6741" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-14a.png" alt="sp2013-14a" width="640" height="298" />

You can now see that the Install account is a member of those roles:

<img class="alignnone size-full wp-image-2489" src="http://mikefrobbins.com/wp-content/uploads/2011/06/sp2010-install6.png" alt="sp2010-install6" width="318" height="268" />

Set the "Max Degree of Parallelism" to 1 on the SQL Server:
<pre class="lang:ps decode:true">Invoke-Sqlcmd -ServerInstance sql01 -Database master –Query `
"EXEC sys.sp_configure N'max degree of parallelism',N'1'
GO
RECONFIGURE WITH OVERRIDE
GO"</pre>
<img class="alignnone size-full wp-image-6747" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2010-install61.png" alt="sp2010-install61" width="611" height="94" />

Changing this setting does not require the SQL Server service to be restarted. Here's where the setting is located at in the GUI:

<img class="alignnone size-full wp-image-6748" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2010-install62.png" alt="sp2010-install62" width="640" height="574" />

If this setting is not changed, you will receive the following error later on during the initial configuration of SharePoint 2013:

<em><span style="color: #ff0000;">New-SPConfigurationDatabase : This SQL Server instance does not have the required "max degree of </span><span style="color: #ff0000;">parallelism" setting of 1. Database provisioning operations will continue to fail if "max degree </span><span style="color: #ff0000;">of parallelism" is not set 1 or the current account does not have permissions to change the </span><span style="color: #ff0000;">setting. See documentation for details on manually changing the setting.
<span style="color: #ff0000;">At line:7 char:1
</span><span style="color: #ff0000;">+ New-SPConfigurationDatabase -DatabaseName $DbName -DatabaseServer $DbServer -Adm ...
<span style="color: #ff0000;">+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
<span style="color: #ff0000;">+ CategoryInfo : InvalidData: (Microsoft.Share...urationDatabase:SPCmdletNewSPConfig</span></span></span></span><span style="color: #ff0000;">urationDatabase) [New-SPConfigurationDatabase], SPException
<span style="color: #ff0000;">+ FullyQualifiedErrorId : Microsoft.SharePoint.PowerShell.SPCmdletNewSPConfigurationDatabase</span></span></em>

<img class="alignnone size-full wp-image-6749" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2010-install63.png" alt="sp2010-install63" width="640" height="248" />

When you’re finished, don’t forget about removing the PSSessions. The following command will remove all PSSessions:

<img class="alignnone size-full wp-image-6653" src="http://mikefrobbins.com/wp-content/uploads/2013/02/appassure123-6.png" alt="appassure123-6" width="404" height="43" />

Login to the SharePoint Web front-end server as the Install account. Run the "SharePoint.exe" executable that was previously downloaded. Select "Install software prerequisites":

<img class="alignnone size-full wp-image-6680" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-5.png" alt="sp2013-5" width="374" height="385" />

The SharePoint server needs to have a connection to the Internet to download the prerequisites, although you could download the prerequisites from another machine and install them manually if necessary.

<img class="alignnone size-full wp-image-6681" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-6.png" alt="sp2013-6" width="640" height="477" />

Once a portion of the prerequisites are installed, the server will need to reboot to continue with the prerequisites installation:

<img class="alignnone size-full wp-image-6682" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-7.png" alt="sp2013-7" width="640" height="477" />

Once the server restarts, log back in with the same user account (the install account). The server will continue with the prerequisites and it will need to reboot one more time after a few more of the prerequisites are installed:

<img class="alignnone size-full wp-image-6683" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-8.png" alt="sp2013-8" width="640" height="507" />

You may briefly see this command window after the restarts when you log back into the server if UAC (<a href="http://en.wikipedia.org/wiki/User_Account_Control" target="_blank">User Access Control</a>) is enabled:

<img class="alignnone size-full wp-image-6711" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-81.png" alt="sp2013-81" width="640" height="72" />

The prerequisites will eventually finish installing:

<img class="alignnone size-full wp-image-6684" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-9.png" alt="sp2013-9" width="640" height="507" />

Now re-run the downloaded SharePoint executable and select “Install SharePoint Foundation”:

<img class="alignnone size-full wp-image-6685" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-10.png" alt="sp2013-10" width="378" height="385" />

Choose the “Complete” installation type:

<img class="alignnone size-full wp-image-6686" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-11.png" alt="sp2013-11" width="613" height="499" />

Select the “Data Location” tab and enter a location for the search index files. This will only be used if you enable the search service on this server, but go ahead and set it just in case:

<img class="alignnone size-full wp-image-6687" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-12.png" alt="sp2013-12" width="616" height="499" />

Uncheck the “Run the SharePoint Products Configuration Wizard now” checkmark:

<img class="alignnone size-full wp-image-6688" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-13.png" alt="sp2013-13" width="617" height="501" />

Test Connectivity to the SQL Server. In my blog on SharePoint 2010, I previously performed this test by adding the telnet client to the SharePoint web front-end server, but that's really unnecessary since you can do the same thing with PowerShell and it eliminates the security risk on having the telnet client installed.
<pre class="lang:ps decode:true">(New-Object System.Net.Sockets.TCPClient("sql01",1433)).Connected</pre>
<img class="alignnone size-full wp-image-6745" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-15a.png" alt="sp2013-15a" width="640" height="54" />

Run the PowerShell script for the initial configuration. This is exactly the same as the script I used with SharePoint 2010.
<pre class="lang:ps decode:true">Add-PSSnapin Microsoft.SharePoint.PowerShell
$FarmCredential = Get-Credential -credential mikefrobbins\spExtranetFarm
$Passphrase = Read-Host -assecurestring "SP PassPhrase"
$DbName = "SP_Extranet_Config"
$DbServer = "sql01.mikefrobbins.com"
$AdminContentDb = "SP_Extranet_AdminContent"
New-SPConfigurationDatabase -DatabaseName $DbName -DatabaseServer $DbServer -AdministrationContentDatabaseName $AdminContentDb -FarmCredentials $FarmCredential -Passphrase $Passphrase
Install-SPHelpCollection -All
Initialize-SPResourceSecurity
Install-SPService
Install-SPFeature -AllExistingFeatures
New-SPCentralAdministration -Port 55000 -WindowsAuthProvider "NTLM"
Install-SPApplicationContent</pre>
<img class="alignnone size-full wp-image-6693" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-16.png" alt="sp2013-16" width="640" height="485" />

Run the following PowerShell script to turn the Application Pool account into a SharePoint managed service account. This accomplishes the same thing as the way I did this in SharePoint 2010, although I've polished the script a little more and taken advantage of the pipeline:
<pre class="lang:ps decode:true">Get-Credential -credential mikefrobbins\spExtranetPool | New-SPManagedAccount</pre>
<img class="alignnone size-full wp-image-6694" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-17.png" alt="sp2013-17" width="640" height="118" />

Create a web application and site collection with PowerShell. There are a couple of changes that need to be made to the script that I used to accomplish this in SharePoint 2010. If you don't use the <em>-AuthenticationProvider</em> parameter, you'll receive the following warning message about the Windows Classic authentication method being deprecated in SharePoint 2013:

<em><span style="color: #ff9900;">WARNING: The Windows Classic authentication method is deprecated in this release and the default behavior of this cmdlet, which creates Windows Classic based web application, is obsolete. It is recommended to use Claims authentication methods. You can create a web application that uses Claims authentication method by specifying the AuthenticationProvider parameter set in this cmdlet. Refer to the http://go.microsoft.com/fwlink/?LinkId=234549 site for more information. Please note that the default behavior of this cmdlet is expected to change in the future release to create a Claims authentication based web application instead of a Windows Classic based web application.</span></em>

<img class="alignnone size-full wp-image-6759" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-171.png" alt="sp2013-171" width="640" height="300" />

The following PowerShell script creates the web application and site collection without any warnings or errors:
<pre class="lang:ps decode:true">$WebAppPool = "SP - Extranet"
$WebAppName = "SP - Extranet"
$WebAppPoolAcct = "mikefrobbins\spExtranetPool"
$WebAppDbName = "SP_Extranet_Content"
$WebAppDbServer = "sql01.mikefrobbins.com"
$WebAppPath = "D:\inetpub\wwwroot\wss\VirtualDirectories\Extranet"
$WebAppPort = "80"
$SiteColUrl = ("http://extranet.mikefrobbins.com")
$SiteColOwner = "mikefrobbins\spExtranetFarm"
$SiteColDescription = "Mike F Robbins - Computing Solutions Extranet"
$SiteColName = "Extranet"
$SiteColTemplate = "STS#0"
New-SPWebApplication -ApplicationPool $WebAppPool -Name $WebAppName -ApplicationPoolAccount (Get-SPManagedAccount $WebAppPoolAcct) `
-DatabaseName $WebAppDbName -DatabaseServer $WebAppDbServer -Path $WebAppPath -Port $WebAppPort -Url $SiteColUrl `
-AuthenticationProvider (New-SPAuthenticationProvider)
New-SPSite -Url $SiteColUrl -OwnerAlias $SiteColOwner -Description $SiteColDescription -Name $SiteColName -Template $SiteColTemplate</pre>
<img class="alignnone size-full wp-image-6695" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-18.png" alt="sp2013-18" width="640" height="545" />

Once you've made the necessary DNS changes, you should be able to access your new SharePoint site, right? Well, not if you're attempting to access it from the SharePoint web front-end server itself. You'll constantly be prompted to login and finally be denied access:

<img class="alignnone size-full wp-image-6698" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-19.png" alt="sp2013-19" width="615" height="383" />

You're experiencing <a href="http://support.microsoft.com/kb/896861" target="_blank">this issue</a> as documented on Microsoft TechNet. Many blogs will point you in the direction of disabling the loopback check, but as the referenced support article says, that's the less than desirable solution.

The following PowerShell script will disable strict name checking and allow BackConnectionHostNames to the specified names:
<pre class="lang:ps decode:true">New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\LanmanServer\Parameters' -Name DisableStrictNameChecking -Value 1 -PropertyType "DWord"

New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0' -Name BackConnectionHostNames -PropertyType MultiString
Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\MSV1_0' -Name BackConnectionHostNames -Value "extranet.mikefrobbins.com"</pre>
<img class="alignnone size-full wp-image-6763" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-20a.png" alt="sp2013-20a" width="640" height="323" />

Once these changes have been made, restart the IISAdmin service and you should be able to access your new SharePoint site from the web front-end server:

<img class="alignnone size-full wp-image-6700" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-21.png" alt="sp2013-21" width="640" height="473" />

By installing SharePoint Foundation 2013 with PowerShell, the database names are much more meaningful than some name with a cryptic GUID on the end of it:

<img class="alignnone size-full wp-image-6766" src="http://mikefrobbins.com/wp-content/uploads/2013/02/sp2013-22.png" alt="sp2013-22" width="184" height="54" />

Looking for more information? See the <a href="http://technet.microsoft.com/en-us/library/jj937863.aspx" target="_blank">Windows PowerShell for SharePoint 2013 learning roadmap</a>.

µ