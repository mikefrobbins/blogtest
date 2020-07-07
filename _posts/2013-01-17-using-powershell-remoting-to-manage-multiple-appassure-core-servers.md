---
ID: 6527
post_title: >
  Using PowerShell Remoting to Manage
  Multiple AppAssure Core Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/01/17/using-powershell-remoting-to-manage-multiple-appassure-core-servers/
published: true
post_date: 2013-01-17 07:30:45
---
In my last blog, I was logged directly into an AppAssure Core server via remote desktop and was running PowerShell commands in the PowerShell console directly on the server. Managing your servers by RDPing directly into them is a bad practice in my opinion. In this blog, I'll explore a few options that could be used to manage an AppAssure Core server with PowerShell without having to resort to logging directly into it with Remote Desktop.

Note: All of the following examples require PowerShell remoting to be enabled on the remote AppAssure Core servers. PowerShell remoting has already been enabled on the servers in the following scenarios by running <em>Enable-PSRemoting</em> on each of the servers. All of the servers in this scenario are running Windows Server 2008 R2, if they were running Windows Server 2012, PowerShell remoting would be enabled by default.

The first option to manage a single AppAssure Core server at a time via PowerShell remoting is to select the "New Remote PowerShell Tab" from the file menu of the PowerShell version 2 or version 3 ISE (Integrated Scripting Environment):

<img class="alignnone size-full wp-image-6528" alt="appassure117-1" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-1.png" width="419" height="335" />

This is basically the same thing as using the <em>Enter-PSSession</em> PowerShell cmdlet to enter a 1 to 1 remoting session in the PowerShell console.

Another option is to use implicit remoting. In the following example, I'm using the <em>New-PSSession</em> cmdlet to create a persistent connection to one of my remote AppAssure Core servers. This portion of the command is inside of the parenthesis so it executes first and then the results of it are used as the input for the Session parameter of <em>Import-PSSessison</em>. The <em>Import-PSSession</em> cmdlet, in this example, is used to import the cmdlets from the AppAssurePowerShellModule from the remote session that was created by the <em>New-PSSession</em> cmdlet into the current session. Later on, I'll show that these imported cmdlets are created as functions temporarily in the local session almost like shortcuts to the actual cmdlets on the remote server that the module is actually located on.
<pre class="lang:ps decode:true">Import-PSSession -Session (New-PSSession -ComputerName Server1) -Module AppAssurePowerShellModule</pre>
<img class="alignnone size-full wp-image-6530" alt="appassure117-2" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-2.png" width="597" height="177" />

If you tried this same syntax on a remote server that has PowerShell version 2 installed, it will generate an error as shown in this example where I'm attempting to import the ActiveDirectory module from a server that only has PowerShell version 2 installed:

<span style="color: #ff0000;">Import-PSSession : Running the Get-Command command in a remote session</span>
<span style="color: #ff0000;">returned no results.</span>
<span style="color: #ff0000;">At line:1 char:1</span>
<span style="color: #ff0000;">+ Import-PSSession -Session (New-PSSession -ComputerName dc01) -Module</span>
<span style="color: #ff0000;">ActiveDire ...</span>
<span style="color: #ff0000;">+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span>
<span style="color: #ff0000;"> + CategoryInfo : InvalidResult: (:) [Import-PSSession], ArgumentE</span><span style="color: #ff0000;">xception</span>
<span style="color: #ff0000;"> + FullyQualifiedErrorId : ErrorNoResultsFromRemoteEnd,Microsoft.PowerShell</span>
<span style="color: #ff0000;"> .Commands.ImportPSSessionCommand</span>

<img class="alignnone size-full wp-image-6557" alt="appassure117-21" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-21.png" width="597" height="201" />

When the remote server is running PowerShell version 2, the module will need to be imported and it's much easier to accomplish with multiple lines instead of a one liner:
<pre class="lang:ps decode:true">$session = New-PSSession -ComputerName dc01
Invoke-Command -Session $session {Import-Module ActiveDirectory}
Import-PSSession -Session $session -Module ActiveDirectory</pre>
<img class="alignnone size-full wp-image-6558" alt="appassure117-22" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-22.png" width="597" height="178" />

Back to our AppAssure scenario, the following example is being run on my local computer which doesn't have the AppAssurePowerShellModule installed. Except for some formatting issues with the default output, you would never know that this module isn't installed:
<pre class="lang:ps decode:true">Get-ProtectedServers | Select DisplayName, Status, Version</pre>
<img class="alignnone size-full wp-image-6531" alt="appassure117-3" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-3.png" width="597" height="454" />

The really cool thing is I can import this same AppAssure PowerShell module from more than one AppAssure Core server, each with different credentials, and use the -Prefix parameter to have different prefixes added to the cmdlets from the different servers:
<pre class="lang:ps decode:true">Import-PSSession -Session (New-PSSession -ComputerName Server1 -Credential (Get-Credential)) -Module AppAssurePowerShellModule -Prefix Site1
Import-PSSession -Session (New-PSSession -ComputerName Server2 -Credential (Get-Credential)) -Module AppAssurePowerShellModule -Prefix Site2
Import-PSSession -Session (New-PSSession -ComputerName Server3 -Credential (Get-Credential)) -Module AppAssurePowerShellModule -Prefix Site3</pre>
<img class="alignnone size-full wp-image-6554" alt="appassure117-4b" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-4b.png" width="597" height="515" />

Now I can run a PowerShell one liner which will execute against the three different AppAssure Core servers that are in different Active Directory domains, use the necessary credentials for each domain, and return results for all of the AppAssure agents:
<pre class="lang:ps decode:true">(Get-Site1ProtectedServers) + (Get-Site2ProtectedServers) + (Get-Site3ProtectedServers) | select displayname, status, version | sort displayname</pre>
<img class="alignnone size-full wp-image-6549" alt="appassure117-5a" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-5a.png" width="597" height="703" />

Here's a list showing the cmdlets from this module that are added to the local computer temporarily as functions which are kind of like shortcuts to the actual cmdlets on the AppAssure servers that I've created the persistent connections to. Notice the cmdlets have site1, site2, and site3 prefixes. This is how I can have the same module imported multiple times and how PowerShell knows which server to send the command to:
<pre class="lang:ps decode:true">Get-Command -Noun site* | sort verb, name | Format-Table name, module -GroupBy verb</pre>
<img class="alignnone size-full wp-image-6550" alt="appassure117-6a" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-6a.png" width="597" height="895" />

The last option that I'll demonstrate and probably the simplest is to use the PowerShell remoting <em style="color: #444444;">Invoke-Command</em> cmdlet for one to many remoting. At first I didn't think I would be able to use <em style="color: #444444;">Invoke-Command</em> due to the requirement in this scenario to use different credentials for each server. I figured it out though, and here's how to use it.

Store each of the persistent connections in a variable using the necessary credentials (different credentials) for each of the three AppAssure Core servers in this scenario:
<pre class="lang:ps decode:true">$site1 = New-PSSession -ComputerName Server1 -Credential (Get-Credential)
$site2 = New-PSSession -ComputerName Server2 -Credential (Get-Credential)
$site3 = New-PSSession -ComputerName Server3 -Credential (Get-Credential)</pre>
<img class="alignnone size-full wp-image-6569" alt="appassure117-7" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-7.png" width="597" height="262" />

Use the -Session parameter with <em>Invoke-Command</em> and<em> </em>specify the variables instead of using the -ComputerName parameter and computer names (What I normally use):
<pre class="lang:ps decode:true">Invoke-Command -Session $site1, $site2, $site3 {Get-ProtectedServers | select displayname, status, version} | select displayname, status, version | sort displayname</pre>
<img class="alignnone size-full wp-image-6570" alt="appassure117-8" src="http://mikefrobbins.com/wp-content/uploads/2013/01/appassure117-8.png" width="597" height="730" />

Automagically, you have results from all three servers using the three different persistent connections that were created and the different credentials are used for each one.

µ