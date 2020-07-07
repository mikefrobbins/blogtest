---
ID: 5563
post_title: >
  Use PowerShell to Create a New Active
  Directory Forest on Windows 2012 Server
  Core Installation (no-GUI)
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/13/use-powershell-to-create-a-new-active-directory-forest-on-windows-2012-server-core-installation-no-gui/
published: true
post_date: 2012-10-13 09:30:39
---
You have a fresh installation of Windows Server 2012 that was installed using the default installation type of server core installation (no-GUI). This server will be the first domain controller in a brand new Active Directory forest.

Log into the server and launch PowerShell by typing "powershell.exe". You'll need to first add the AD-Domain-Services role to the server:
<pre class="lang:ps decode:true">Add-WindowsFeature AD-Domain-Services</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest0.jpg"><img class="alignnone size-full wp-image-5565" title="PoSH-newADForest0" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest0.jpg" width="612" height="124" /></a>

The installation of this role completes and a restart is not required:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest1.jpg"><img class="alignnone size-full wp-image-5564" title="PoSH-newADForest1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest1.jpg" width="640" height="132" /></a>

Now to make this server the first domain controller in a new forest:
<pre class="lang:ps decode:true">Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath 'C:\Windows\NTDS' -DomainMode 'Win2012' -DomainName 'mikefrobbins.com' -DomainNetbiosName 'MIKEFROBBINS' -ForestMode 'Win2012' -InstallDns:$true -LogPath 'C:\Windows\NTDS' -NoRebootOnCompletion:$true -SysvolPath 'C:\Windows\SYSVOL' -Force:$true</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest2.jpg"><img class="alignnone size-full wp-image-5566" title="PoSH-newADForest2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest2.jpg" width="640" height="101" /></a>

The installation will go through several steps:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest3.jpg"><img class="alignnone size-full wp-image-5567" title="PoSH-newADForest3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest3.jpg" width="640" height="321" /></a>

A restart is required when the installation is complete:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest4.jpg"><img class="alignnone size-full wp-image-5568" title="PoSH-newADForest4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/posh-newadforest4.jpg" width="640" height="527" /></a>

µ