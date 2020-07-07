---
ID: 2955
post_title: >
  Terminal Server Related PowerShell
  Scripts
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/10/13/terminal-server-related-powershell-scripts/
published: true
post_date: 2011-10-13 07:30:41
---
Thought I would post a couple of PowerShell scripts that I’ve recently written. Both of these scripts were written specifically for terminal servers but they can be modified as needed. The first one finds what terminal servers a user is logged into. It retrieves a list of terminal server names from the specified OU. I started out by using the Get-TSServers cmdlet for the list of servers, but that cmdlet takes a while and you have more control by just using the Get-ADComputer cmdlet since your terminal servers are more than likely in their own OU anyway. You need to use a For-Each loop since you can't specify multiple computer names with the Get-TSSession cmdlet. Looking at the help for that cmdlet shows that all three parameter sets start with: <em>Get-TSSession [-ComputerName &lt;String&gt;]</em>. If it showed: <em>Get-TSSession [-ComputerName &lt;String<span style="color: #ff0000;">[]</span><span style="color: #000000;">&gt;</span>]</em> instead, multiple computer names would be able to be entered and there would be no need for the For-Each loop.

#Prerequisites:
#Download and install the "<a href="http://archive.msdn.microsoft.com/PSTerminalServices/Release/ProjectReleases.aspx?ReleaseId=5479" target="_blank">Terminal Services PowerShell Module</a>" (by <a href="http://blogs.microsoft.co.il/blogs/ScriptFanatic/" target="_blank">Shay Levy</a>)
#For Windows 7, Download and install the "<a href="http://www.microsoft.com/download/en/details.aspx?displaylang=en&amp;id=7887" target="_blank">Remote Server Administration Tools</a>"
#Enable the "Active Directory Module for Windows PowerShell" feature
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
Import-Module PSTerminalServices
$user = Read-Host "Enter a user name"
$servers = Get-ADComputer -Filter * -SearchBase "ou=terminal servers,ou=computers,ou=test,dc=mikefrobbins,dc=com" | Select-Object -ExpandProperty Name
ForEach ($server in $servers) {
Get-TSSession -ComputerName $server -Username $user
}</pre>
This second one finds who is running a specific process on the terminal servers such as “msaccess.exe” (Microsoft Access). It could actually be used for any machine that supports PowerShell since it doesn't contain any cmdlets that are specific to terminal server. This example also demonstrates how to change the column header names.

#Prerequisites:
#For Windows 7, Download and install the "<a href="http://www.microsoft.com/download/en/details.aspx?displaylang=en&amp;id=7887" target="_blank">Remote Server Administration Tools</a>"
#Enable the "Active Directory Module for Windows PowerShell" feature
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
$process = Read-Host "Enter a process name"
$servers = Get-ADComputer -Filter * -SearchBase "ou=terminal servers,ou=computers,ou=test,dc=mikefrobbins,dc=com" | Select-Object -ExpandProperty Name
get-wmiobject win32_process -computer $servers | where {$_.name -eq $process} | select  @{l='Server';e={$_.__server}}, @{l='User';e={$_.getowner().user}}, @{l='Process';e={$_.name}} | sort Server, User</pre>
Here's an updated version of the second script based on <a href="http://jdhitsolutions.com/blog/" target="_blank">Jeffery Hicks</a> comments:
<pre class="lang:ps decode:true">Import-Module ActiveDirectory
$process = Read-Host "Enter a process name"
$servers = Get-ADComputer -Filter * -SearchBase "ou=terminal servers,ou=computers,ou=test,dc=mikefrobbins,dc=com" | Select-Object -ExpandProperty Name
get-wmiobject win32_process -filter "name='$process'" -computer $servers | select  @{l='Server';e={$_.__server}}, @{l='User';e={$_.getowner().user}}, @{l='Process';e={$_.name}} | sort Server, User</pre>
µ