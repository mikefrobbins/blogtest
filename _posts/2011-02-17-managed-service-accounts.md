---
ID: 1886
post_title: Managed Service Accounts
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/02/17/managed-service-accounts/
published: true
post_date: 2011-02-17 07:30:29
---
<a href="http://technet.microsoft.com/en-us/library/ff641731(WS.10).aspx" target="_blank">Managed Service Accounts</a> seem to be the end all and fix all for those services such as Exchange or SQL that we have all at some point either set to run as local system, an administrator account, or at best a domain user account that has been setup with the <a href="http://en.wikipedia.org/wiki/Principle_of_least_privilege" target="_blank">principal of least privilege</a>. Using an account such as local system grants more rights than necessary and the service ends up running as a local administrator equivalent. Using a normal domain user account, even if it has been setup with the principle of least privilege, still leaves a lot to be desired since password management of the account is an ongoing security problem. The solution is a Managed Service Account which is a new feature of Windows Server 2008 R2 and Windows 7. A domain that is operating at a functional level below 2008 R2 is able to take advantage of the automatic password management feature of MSA's, one operating at the 2008 R2 level is also able to take advantage of the automatic SPN (Service Principal Name) management feature.

To create a managed service account, open PowerShell as a user with permissions to update Active Directory and run the <em>Import-Module ActiveDirectory</em> cmdlet.<em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg"><img class="alignnone size-full wp-image-1887" title="msa1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg" width="350" height="86" /></a> </em>

If you’re running this on a non-domain controller and you receive the error in the image below, you’ll need to install the Active Directory module for Windows PowerShell.
<em><span style="color: #ff0000;">Import-Module : The specified module 'ActiveDirectory' was not loaded because no valid module file was found in any module directory. At line:1 char:14 + Import-Module &lt;&lt;&lt;&lt;  ActiveDirectory + CategoryInfo : ResourceUnavailable: (ActiveDirectory:String) [Import-Module], FileNotFoundException + FullyQualifiedErrorId : Modules_ModuleNotFound,Microsoft.PowerShell.Commands.ImportModuleCommand</span></em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa2.jpg"><img class="alignnone size-full wp-image-1888" title="msa2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa2.jpg" width="640" height="91" /></a>

Run the <em>Import-Module ServerManager </em>cmdlet.<em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa3.jpg"><img class="alignnone size-full wp-image-1889" title="msa3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa3.jpg" width="305" height="71" /></a></em>

Run the <em>Add-WindowsFeature RSAT-AD-PowerShell </em>cmdlet to install the Active Directory module for Windows PowerShell.<em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa4.jpg"><img class="alignnone size-full wp-image-1890" title="msa4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa4.jpg" width="639" height="133" /></a></em>

Close and re-open PowerShell, otherwise you may receive the error in the image below:
<em><span style="color: #ff0000;">Attempting to perform the InitializeDefaultDrives operation on the 'ActiveDirectory' provider failed.</span></em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa5.jpg"><img class="alignnone size-full wp-image-1891" title="msa5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa5.jpg" width="640" height="56" /></a>

You should now be able to run the <em>Import-Module ActiveDirectory </em>cmdlet without error.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg"><img class="alignnone size-full wp-image-1887" title="msa1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg" width="350" height="86" /></a>

Run the new service account cmdlet using the following syntax:
<pre class="lang:ps decode:true">New-ADSericeAccount –Name “&lt;account name&gt;” –Enabled $true –Path “&lt;path to organizational unit&gt;”</pre>
In the example shown in the image below, the command is:
<pre class="lang:ps decode:true">New-ADServiceAccount –Name “svcWebSite” –Enabled $true –Path “cn=Managed Service Accounts,dc=mikefrobbins,dc=com”</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa6.jpg"><img class="alignnone size-full wp-image-1892" title="msa6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa6.jpg" width="640" height="36" /></a>

If you did not receive any error messages, the new Managed Service Account should appear in the specified OU in Active Directory Users and Computers.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa7.jpg"><img class="alignnone size-medium wp-image-1893" title="msa7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa7.jpg?w=300" width="300" height="140" /></a>

Link the service account to the computer you want to use it on using the following syntax:
<pre class="lang:ps decode:true">Add-ADComputerServiceAccount –Identity “&lt;the computer that will use the MSA&gt;” –ServiceAccount “&lt;service account name&gt;”</pre>
In the example shown in the image below, the command is:
<pre class="lang:ps decode:true">Add-ADComputerServiceAccount –Identity “WEB” –ServiceAccount “svcWebSite”</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa8.jpg"><img class="alignnone size-full wp-image-1894" title="msa8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa8.jpg" width="628" height="55" /></a>

Open PowerShell on the computer where you want to use the Managed Service Account and import the Active Directory modules:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg"><img class="alignnone size-full wp-image-1887" title="msa1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa1.jpg" width="350" height="86" /></a>

Install the MSA on the computer you want to use it on using the following syntax:
<pre class="lang:ps decode:true">Install-ADServiceAccount –Identity &lt;MASName&gt;</pre>
In the example shown in the image below, the command is:
<pre class="lang:ps decode:true">Install-ADServiceAccount –Identity “svcWebSite”</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa9.jpg"><img class="alignnone size-full wp-image-1895" title="msa9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa9.jpg" width="451" height="58" /></a>

If you receive the error in the image below, it's due to either not having the necessary permissions to update AD or <a href="http://en.wikipedia.org/wiki/User_Account_Control" target="_blank">UAC</a> preventing the command from running.
<em><span style="color: #ff0000;">Install-ADServiceAccount : Cannot install service account. Error Message: 'Unknown error (0xc0000022)'. At line:1 char:25 + Install-ADServiceAccount &lt;&lt;&lt;&lt;  -Identity "svcWebSite"+ CategoryInfo : WriteError: (svcWebSite:String) [Install-ADServiceAccount], ADException + FullyQualifiedErrorId : InstallADServiceAccount:PerformOperation:InstallServiceAcccountFailure,Microsoft. ActiveDirectory.Management.Commands.InstallADServiceAccount</span></em>
<span style="font-style: normal;"><a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa11.jpg"><img class="alignnone size-full wp-image-1934" title="msa11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa11.jpg" width="640" height="90" /></a></span>

Try right clicking on the Windows PowerShell icon and selecting "Run as Administrator" if you received the error above.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa12.jpg"><img class="alignnone size-full wp-image-1935" title="msa12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa12.jpg" width="230" height="194" /></a>

Now enter the service account which ends with a $ where you want to use it without a password as shown in the example below:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa10.jpg"><img class="alignnone size-medium wp-image-1896" title="msa10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa10.jpg?w=300" width="300" height="265" /></a>

A Managed Service Account automatically changes its password every thirty days by default so password management is no longer an issue.

µ