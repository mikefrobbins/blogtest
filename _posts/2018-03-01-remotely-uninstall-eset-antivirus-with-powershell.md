---
ID: 16420
post_title: >
  Remotely Uninstall ESET Antivirus with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/03/01/remotely-uninstall-eset-antivirus-with-powershell/
published: true
post_date: 2018-03-01 07:30:11
---
Recently, one of the companies that I provide support for switched from using ESET to a new antivirus vendor. The problem is that all of their servers had both ESET File Security and the ESET Remote Administrator Agent installed which needed to be uninstalled before installing the new antivirus agent.

I determined that the following commands could be used to uninstall the applications.
<pre class="lang:ps decode:true">#Uninstall Eset Remote Administrator Agent
sc.exe delete eraagentsvc
msiexec.exe /qn /x {41F12F70-5FA9-43F5-94F4-53B54EB4EEC4}

#Uninstall Eset File Security
msiexec.exe /qn /x {22ED011A-E075-4D3D-AE41-E00F4372470A}</pre>
Running msiexec.exe /? shows the available options.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/03/eset-uninstall1a.png"><img class="alignnone size-full wp-image-16421" src="http://mikefrobbins.com/wp-content/uploads/2018/03/eset-uninstall1a.png" alt="" width="401" height="450" /></a>

Based on this information, it appears that /x is to uninstall and /qn is for no user input. The uninstall of ESET File Security using the previous commands that I provided cause the system to reboot automatically. There appears to be a switch for msiexec.exe to suppress the reboot, but it's not something that I tried since the removal process does indeed require a restart.

I initially wrapped those commands inside of the <em>Invoke-Command</em> cmdlet to remotely remove those two applications, but the problem that I ran into is the remoting session didn't wait long enough for the uninstall to complete before ending the session.

The solution was to use <em>Get-Process</em> inside of <em>Invoke-Command</em> with the <em>Wait</em> parameter to allow the uninstall to complete before the remoting session ended.
<pre class="lang:ps decode:true">#Uninstall Eset - Warning: This will reboot the server when complete
Invoke-Command -ComputerName Server01, Server02, Server03 {

    #Uninstall Eset Remote Administrator Agent
    Start-Process 'sc.exe' -ArgumentList 'delete eraagentsvc' -Wait
    Start-Process 'msiexec.exe' -ArgumentList '/qn /x {41F12F70-5FA9-43F5-94F4-53B54EB4EEC4}' -Wait

    #Uninstall Eset File Security
    Start-Process 'msiexec.exe' -ArgumentList '/qn /x {22ED011A-E075-4D3D-AE41-E00F4372470A}' -Wait

} -Credential (Get-Credential)</pre>
You could use <em>Get-Content</em> to read from a list of server names in a text file or <em>Get-ADComputer</em> to read server names from Active Directory.

You could also query the event logs of those remote servers to verify that the applications were indeed uninstalled.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName Server01, Server02, Server03 {
    Get-WinEvent -FilterHashtable @{LogName = 'Application'; ID = '1034'; ProviderName = 'MsiInstaller'; StartTime = (Get-Date).AddDays(-1)}
} -Credential (Get-Credential)</pre>
While <em>Get-WinEvent</em> has a <em>ComputerName</em> parameter, it's much more likely that it will be blocked by a firewall on your network or that the necessary ports to use it won't be open on the server that you're querying. You'll avoid these problems by wrapping it inside of <em>Invoke-Command</em> instead. This also allows all of the remote systems to be queried at once (up to 32 at once by default) instead of one at a time.

µ