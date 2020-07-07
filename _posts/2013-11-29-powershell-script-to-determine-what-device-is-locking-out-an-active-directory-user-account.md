---
ID: 8626
post_title: >
  PowerShell Script to Determine What
  Device is Locking Out an Active
  Directory User Account
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/29/powershell-script-to-determine-what-device-is-locking-out-an-active-directory-user-account/
published: true
post_date: 2013-11-29 07:30:48
---
I recently received a request to determine why a specific user account was constantly being locked out after changing their Active Directory password and while I've previously written scripts to accomplish this same type of task, I decided to write an updated script.

Active Directory user account lockouts are replicated to the PDC emulator in the domain through emergency replication and while I could have used the <em>Get-ADDomain</em> cmdlet to easily determine the PDC emulator for the domain:
<pre class="lang:ps decode:true">(Get-ADDomain).PDCEmulator</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/pdc-emulator.png"><img class="alignnone size-full wp-image-8677" src="http://mikefrobbins.com/wp-content/uploads/2013/11/pdc-emulator.png" alt="pdc-emulator" width="560" height="76" /></a>

That would have had a dependency of requiring the RSAT tools to be installed on the workstation this script was being run from so I decided to use the .NET framework to accomplish that particular task to eliminate the Active Directory module dependency:
<pre class="wrap:false lang:ps decode:true">[System.DirectoryServices.ActiveDirectory.Domain]::GetDomain((
    New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Domain', $env:USERDNSDOMAIN))
).PdcRoleOwner.name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/pdc-emulator-netframework.png"><img class="alignnone size-full wp-image-8686" src="http://mikefrobbins.com/wp-content/uploads/2013/11/pdc-emulator-netframework.png" alt="pdc-emulator-netframework" width="877" height="111" /></a>

If you're interested in using the .NET framework to determine the Active Directory FSMO role holders with PowerShell, I wrote a blog article titled "<a href="http://mikefrobbins.com/2013/04/18/powershell-function-to-determine-the-active-directory-fsmo-role-holders-via-the-net-framework/" target="_blank">PowerShell Function to Determine the Active Directory FSMO Role Holders via the .NET Framework</a>" that covers that subject in more detail.

This "<em>Get-LockedOutUser.ps1</em>" script allows you to specify the following via parameter input to narrow down the results:
<ul>
	<li>Specific userid, defaulting to all locked out userid's</li>
	<li>Start time to begin searching records for, defaulting to the last three days</li>
	<li>Domain name to search for lockouts in, defaulting to the user's domain who is running the script</li>
</ul>
<pre class="wrap:false lang:ps decode:true" title="Get-LockedOutUser.ps1">#Requires -Version 3.0
&lt;#
.SYNOPSIS
    Get-LockedOutUser.ps1 returns a list of users who were locked out in Active Directory.

.DESCRIPTION
    Get-LockedOutUser.ps1 is an advanced script that returns a list of users who were locked out in Active Directory
by querying the event logs on the PDC emulator in the domain.

.PARAMETER UserName
    The userid of the specific user you are looking for lockouts for. The default is all locked out users.

.PARAMETER StartTime
    The datetime to start searching from. The default is all datetimes that exist in the event logs.

.EXAMPLE
    Get-LockedOutUser.ps1

.EXAMPLE
    Get-LockedOutUser.ps1 -UserName 'mikefrobbins'

.EXAMPLE
    Get-LockedOutUser.ps1 -StartTime (Get-Date).AddDays(-1)

.EXAMPLE
    Get-LockedOutUser.ps1 -UserName 'mikefrobbins' -StartTime (Get-Date).AddDays(-1)
#&gt;

[CmdletBinding()]
param (
    [ValidateNotNullOrEmpty()]
    [string]$DomainName = $env:USERDOMAIN,

    [ValidateNotNullOrEmpty()]
    [string]$UserName = "*",

    [ValidateNotNullOrEmpty()]
    [datetime]$StartTime = (Get-Date).AddDays(-3)
)

Invoke-Command -ComputerName (

    [System.DirectoryServices.ActiveDirectory.Domain]::GetDomain((
        New-Object System.DirectoryServices.ActiveDirectory.DirectoryContext('Domain', $DomainName))
    ).PdcRoleOwner.name

) {

    Get-WinEvent -FilterHashtable @{LogName='Security';Id=4740;StartTime=$Using:StartTime} |
    Where-Object {$_.Properties[0].Value -like "$Using:UserName"} |
    Select-Object -Property TimeCreated, 
        @{Label='UserName';Expression={$_.Properties[0].Value}},
        @{Label='ClientName';Expression={$_.Properties[1].Value}}

} -Credential (Get-Credential) |
Select-Object -Property TimeCreated, UserName, ClientName</pre>
The script prompts for alternate credentials because in my opinion, you shouldn't be running your PowerShell session with elevated credentials:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device0.png"><img class="alignnone size-full wp-image-8684" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device0.png" alt="ad-lockout-device0" width="560" height="390" /></a>

As you can see, jimmy0 is being locked out by a device named PC01:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device1.png"><img class="alignnone size-full wp-image-8679" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device1.png" alt="ad-lockout-device1" width="646" height="254" /></a>

The StartTime parameter can be used to specify more or fewer days if the default of three days doesn't meet your needs as shown in the following example where one day is used:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device2.png"><img class="alignnone size-full wp-image-8681" src="http://mikefrobbins.com/wp-content/uploads/2013/11/ad-lockout-device2.png" alt="ad-lockout-device2" width="705" height="214" /></a>

The script shown in this blog can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/Determine-What-Device-is-325d9720" target="_blank">script repository</a>.

<span style="text-decoration: underline;">Update 08/30/15</span>
I've posted an updated version of this script which is now a PowerShell function that includes error handling that can be downloaded from my <a href="https://github.com/mikefrobbins/ActiveDirectory" target="_blank">Active Directory repository on GitHub</a>.

µ