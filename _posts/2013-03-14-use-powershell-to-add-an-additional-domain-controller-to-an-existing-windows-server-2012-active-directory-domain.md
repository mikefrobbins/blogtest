---
ID: 6921
post_title: >
  Use PowerShell to add an additional
  Domain Controller to an existing Windows
  Server 2012 Active Directory Domain
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/03/14/use-powershell-to-add-an-additional-domain-controller-to-an-existing-windows-server-2012-active-directory-domain/
published: true
post_date: 2013-03-14 07:30:58
---
Recently, I decided to add a second domain controller to my mikefrobbins.com domain. The existing server and this new server that will become a domain controller both run the Microsoft Windows Server 2012 operating system and both were installed with the default installation type of server core (no GUI).

Even though the GUI can be turned on and off in Windows Server 2012 (unlike in Windows Server 2008 and 2008 R2), I prefer not to add the GUI unless absolutely necessary.

You've already loaded the base operating system, added it to the domain, and configured the server as per your organization's standards. Log into the new server you want to add as an additional domain controller and launch PowerShell by typing “powershell.exe”. You’ll need to first add the AD-Domain-Services role to the server:
<pre class="lang:ps decode:true">Add-WindowsFeature AD-Domain-Services</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest0.jpg"><img class="alignnone size-full wp-image-5565" alt="PoSH-newADForest0" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest0.jpg" width="612" height="124" /></a>

The installation of this role completes and a restart is not required:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest1.jpg"><img class="alignnone size-full wp-image-5564" alt="PoSH-newADForest1" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest1.jpg" width="640" height="132" /></a>

Now to make this server an additional domain controller in the mikefrobbins.com domain:
<pre class="lang:ps decode:true">Install-ADDSDomainController -CreateDnsDelegation:$false -DatabasePath 'C:\Windows\NTDS' -DomainName 'mikefrobbins.com' -InstallDns:$true -LogPath 'C:\Windows\NTDS' -NoGlobalCatalog:$false -SiteName 'Default-First-Site-Name' -SysvolPath 'C:\Windows\SYSVOL' -NoRebootOnCompletion:$true -Force:$true</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc1.png"><img class="alignnone size-full wp-image-6922" alt="add-dc1" src="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc1.png" width="640" height="104" /></a>

The installation will go through several steps:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc2.png"><img class="alignnone size-full wp-image-6923" alt="add-dc2" src="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc2.png" width="640" height="322" /></a>

A restart is required when the installation is complete:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc3.png"><img class="alignnone size-full wp-image-6924" alt="add-dc3" src="http://mikefrobbins.com/wp-content/uploads/2013/03/add-dc3.png" width="640" height="526" /></a>

If you're looking to install the first domain controller in a new Active Directory forest instead of adding an additional domain controller in an existing domain, see my blog article titled "<a href="http://mikefrobbins.com/2012/10/13/use-powershell-to-create-a-new-active-directory-forest-on-windows-2012-server-core-installation-no-gui/" target="_blank">Use PowerShell to Create a New Active Directory Forest on Windows 2012 Server Core Installation (no-GUI)</a>".

µ