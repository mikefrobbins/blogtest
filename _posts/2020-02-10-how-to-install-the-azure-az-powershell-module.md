---
ID: 18274
post_title: >
  How to Install the Azure Az PowerShell
  Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/02/10/how-to-install-the-azure-az-powershell-module/
published: true
post_date: 2020-02-10 07:30:58
---
The Az PowerShell module was released in December of 2018 and is now the recommended module for managing Microsoft Azure. AzureRM is the previous PowerShell module for managing Azure which has been deprecated but will continue to be supported until December of 2020.

Windows PowerShell 5.1, PowerShell Core 6, PowerShell 7, and higher are supported by the Az PowerShell module. Windows 10 version 1607 and higher has Windows PowerShell 5.1 installed by default.

Determine if the version of PowerShell you're running meets the minimum requirements before attempting to install the Az PowerShell module.
<pre class="lang:ps decode:true ">$PSVersionTable.PSVersion</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install1a.jpg"><img class="alignnone size-full wp-image-18275" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install1a.jpg" alt="" width="859" height="133" /></a>

The .NET Framework 4.7.2 or higher is also required by the Az PowerShell module on Windows. The version of the .NET Framework needs to be verified prior to installing the Az PowerShell module because Windows PowerShell 5.1 only requires the .NET Framework 4.5 or higher.

Verify the .NET Framework 4.7.2 or higher is installed.
<pre class="lang:ps decode:true ">(Get-ItemProperty -Path 'HKLM:\Software\Microsoft\NET Framework Setup\NDP\v4\Full' -ErrorAction SilentlyContinue).Release -ge 461808</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install2a.jpg"><img class="alignnone size-full wp-image-18279" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install2a.jpg" alt="" width="859" height="91" /></a>

Previously there were full and client versions of the NET Framework, but beginning with .NET Framework 4.5, the client version was discontinued so there's no longer a reason to check both registry entries.

The installation itself is simple from the PowerShell Gallery.
<pre class="lang:ps decode:true ">Install-Module -Name Az -Force</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install3a.jpg"><img class="alignnone size-full wp-image-18281" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install3a.jpg" alt="" width="859" height="187" /></a>

The previous command will install the Az module for all users and requires admin rights. The official Microsoft documentation recommends only installing it for the current user by specifying the <em>Scope</em> parameter with <em>CurrentUser</em> as its value which does not require admin rights.

The official Microsoft documentation also states that you can't have both the AzureRM and Az PowerShell modules installed for Windows PowerShell 5.1 at the same time. Their recommendation is to install the Az module for PowerShell Core 6 or PowerShell 7 if you already have the AzureRM module installed for Windows PowerShell.
<blockquote>Install them both in Windows PowerShell at your own risk!</blockquote>
Clearly, you can have them both installed at the same time as shown in the following example.
<pre class="lang:ps decode:true">Get-InstalledModule -Name Az, AzureRM</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install4a.jpg"><img class="alignnone size-full wp-image-18300" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install4a.jpg" alt="" width="859" height="157" /></a>

While they didn't specify why, many assume that it's due to the ability to enable AzureRM aliases in the Az module for backwards compatibility. That conclusion is completely fabricated and isn't a factor due to the way command precedence works in PowerShell. If commands with the same name exist, alias are executed first, followed by functions, cmdlets, and then native Windows commands.

Let's see what happens when I have both modules installed and enable AzureRM aliases in the Az PowerShell module.
<pre class="lang:ps decode:true">Enable-AzureRmAlias</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install7a.jpg"><img class="alignnone size-full wp-image-18304" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install7a.jpg" alt="" width="859" height="60" /></a>

Notice that although I have both modules installed, when I execute an AzureRM command, it actually executes the command in the Az module via its alias instead of the command in the AzureRM module.
<pre class="lang:ps decode:true">Trace-Command -Expression {Connect-AzureRmAccount} -Name CommandDiscovery -PSHost | Out-Null</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install6a.jpg"><img class="alignnone size-full wp-image-18303" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install6a.jpg" alt="" width="859" height="116" /></a>

If the AzureRM module is imported first, an error will be generated when you try to import the Az module.
<pre class="lang:ps decode:true ">Import-Module -Name Az</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install8a.jpg"><img class="alignnone size-full wp-image-18308" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install8a.jpg" alt="" width="859" height="355" /></a>

If you attempt to import the AzureRM module after the Az module, a different error is generated.
<pre class="lang:ps decode:true ">Import-Module -Name AzureRM</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install9a.jpg"><img class="alignnone size-full wp-image-18314" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install9a.jpg" alt="" width="859" height="215" /></a>

Removing the module within the same session and then trying to import the other one doesn't clear up the problem either. If you are going to have both installed for some reason in Windows PowerShell, you'll either need to close and reopen your PowerShell session when switching between the two or have two separate sessions open.

If you no longer need the AzureRM module, you always have the option of de-installing it as referenced in one of the previous error messages. If you don't use the <em>Uninstall-AzureRm</em> command and decide to use another method to de-install the AzureRM module, be sure to also remove the other 53 modules that it installs.
<pre class="lang:ps decode:true">Get-InstalledModule -Name AzureRM*</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install5a.jpg"><img class="alignnone size-full wp-image-18301" src="https://mikefrobbins.com/wp-content/uploads/2020/02/az-install5a.jpg" alt="" width="859" height="886" /></a>

Microsoft articles containing addition information:
<ul>
 	<li><a href="https://docs.microsoft.com/en-us/powershell/scripting/install/windows-powershell-system-requirements" target="_blank" rel="noopener noreferrer">Windows PowerShell System Requirements</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed" target="_blank" rel="noopener noreferrer">How to: Determine which .NET Framework versions are installed</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/dotnet/framework/deployment/client-profile" target="_blank" rel="noopener noreferrer">.NET Framework Client Profile</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/powershell/azure/new-azureps-module-az" target="_blank" rel="noopener noreferrer">Introducing the new Azure PowerShell Az module</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/powershell/azure/install-az-ps" target="_blank" rel="noopener noreferrer">Install the Azure PowerShell module</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_command_precedence" target="_blank" rel="noopener noreferrer">About Command Precedence</a></li>
 	<li><a href="https://docs.microsoft.com/en-us/powershell/azure/migrate-from-azurerm-to-az" target="_blank" rel="noopener noreferrer">Migrate Azure PowerShell from AzureRM to Az</a></li>
</ul>
Be sure to read my previous blog article "<a href="https://mikefrobbins.com/2020/01/28/setting-dependencies-on-the-azure-powershell-module/" target="_blank" rel="noopener noreferrer">Setting Dependencies on the Azure PowerShell Module</a>" which shows that the Az module is simply a wrapper containing 54 PowerShell modules for individual Azure products.

µ