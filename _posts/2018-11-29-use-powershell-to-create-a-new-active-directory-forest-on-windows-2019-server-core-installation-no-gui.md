---
ID: 17408
post_title: >
  Use PowerShell to Create a New Active
  Directory Forest on Windows 2019 Server
  Core Installation (no-GUI)
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/11/29/use-powershell-to-create-a-new-active-directory-forest-on-windows-2019-server-core-installation-no-gui/
published: true
post_date: 2018-11-29 07:30:35
---
You have a fresh installation of Windows Server 2019 that was installed using the default installation type of server core installation (no-GUI). This server will be the first domain controller in a brand new Active Directory forest. You've completed the following configuration prior to attempting to turn this server into a domain controller:
<ul>
 	<li>Install all the available Windows Updates</li>
 	<li>Set the time zone</li>
 	<li>Set the computer name</li>
 	<li>Set a static IP address</li>
</ul>
Log into the server and launch PowerShell by typing "powershell.exe". You’ll need to first add the <em>AD-Domain-Services</em> role to the server:
<pre class="lang:ps decode:true ">Install-WindowsFeature -Name AD-Domain-Services</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain1a.jpg"><img class="alignnone size-full wp-image-17409" src="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain1a.jpg" alt="" width="857" height="160" /></a>

Store the SafeMode admin password in a variable. Per the documentation, this "<em>Supplies the password for the administrator account when the computer is started in Safe Mode or a variant of Safe Mode, such as Directory Services Restore Mode.</em>"
<pre class="lang:ps decode:true">$Password = Read-Host -Prompt   'Enter SafeMode Admin Password' -AsSecureString</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain2a.jpg"><img class="alignnone size-full wp-image-17410" src="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain2a.jpg" alt="" width="857" height="81" /></a>

Now to make this server the first domain controller in a new forest:
<pre class="lang:ps decode:true ">Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath C:\Windows\NTDS -DomainMode WinThreshold -DomainName mikefrobbins.com -DomainNetbiosName MIKEFROBBINS -ForestMode WinThreshold -InstallDns:$true -LogPath C:\Windows\NTDS -NoRebootOnCompletion:$true -SafeModeAdministratorPassword $Password -SysvolPath C:\Windows\SYSVOL -Force:$true
</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain3a.jpg"><img class="alignnone size-full wp-image-17411" src="https://mikefrobbins.com/wp-content/uploads/2018/11/newaddomain3a.jpg" alt="" width="857" height="636" /></a>

You could also use the previous command with splatting to make it a little easier on the eyes instead of a long one-liner.
<pre class="lang:ps decode:true">$Params = @{
    CreateDnsDelegation = $false
    DatabasePath = 'C:\Windows\NTDS'
    DomainMode = 'WinThreshold'
    DomainName = 'mikefrobbins.com'
    DomainNetbiosName = 'MIKEFROBBINS'
    ForestMode = 'WinThreshold'
    InstallDns = $true
    LogPath = 'C:\Windows\NTDS'
    NoRebootOnCompletion = $true
    SafeModeAdministratorPassword = $Password
    SysvolPath = 'C:\Windows\SYSVOL'
    Force = $true
}

Install-ADDSForest @Params</pre>
There's not a new domain or forest functional level for Windows Server 2019 so a value of "WinThreshold" or 7 puts it in Windows Server 2016 mode. The valid values are:
<ul>
 	<li>Default</li>
 	<li>Windows Server 2003: "Win2003" or "2"</li>
 	<li>Windows Server 2008: "Win2008" or "3"</li>
 	<li>Windows Server 2008 R2: Win2008R2 or "4"</li>
 	<li>Windows Server 2012: "Win2012" or "5"</li>
 	<li>Windows Server 2012 R2: "Win2012R2" or "6"</li>
 	<li>Windows Server 2016: "WinThreshold" or "7"</li>
</ul>
Per the documentation. <em>"The domain functional level cannot be lower than the forest functional level, but it can be higher."</em>

µ