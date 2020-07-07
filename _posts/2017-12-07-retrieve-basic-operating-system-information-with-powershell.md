---
ID: 15956
post_title: >
  Retrieve Basic Operating System
  Information with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/12/07/retrieve-basic-operating-system-information-with-powershell/
published: true
post_date: 2017-12-07 07:30:37
---
PowerShell version 5.1 added a new cmdlet named <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-computerinfo?view=powershell-5.1" target="_blank" rel="noopener"><em>Get-ComputerInfo</em></a> which retrieves lots of information from the local computer. It can be wrapped inside of the PowerShell remoting <em>Invoke-Command</em> cmdlet to retrieve that same information from remote computers.

My biggest complaint with <em>Get-ComputerInfo</em> is that it takes forever to return the information and then when it does finally complete, it's missing values for most of its properties. Also, if you're going to wrap it inside of <em>Invoke-Command</em>, then all of your remote machines would need PowerShell 5.1 or higher installed.

Because of these shortcomings, I decided to write my own simple function to retrieve just the operating system information I wanted while relying on CIM sessions for remote connectivity so down level clients could also be queried.
<pre class="lang:ps decode:true " title="Get-MrOSInfo">#Requires -Version 3.0
function Get-MrOSInfo {

&lt;#
.SYNOPSIS
    Gets basic operating system properties.
 
.DESCRIPTION
    The Get-MrOSInfo function gets basic operating system properties for the local computer or for one or more remote
    computers.
 
 .PARAMETER CimSession
    Specifies the CIM session to use for this function. Enter a variable that contains the CIM session or a command that
    creates or gets the CIM session, such as the New-CimSession or Get-CimSession cmdlets. For more information, see
    about_CimSessions.

.EXAMPLE
    Get-MrOSInfo

.EXAMPLE
    Get-MrOSInfo -CimSession (New-CimSession -ComputerName Server01, Server02)

.INPUTS
    None
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Microsoft.Management.Infrastructure.CimSession[]]$CimSession
    )

    $Params = @{}

    if ($PSBoundParameters.CimSession) {
        $Params.CimSession = $CimSession
    }
   
    $OSInfo = Get-CimInstance @Params -ClassName Win32_OperatingSystem -Property Caption, BuildNumber, OSArchitecture, CSName

    $OSVersion = Invoke-CimMethod @Params -Namespace root\cimv2 -ClassName StdRegProv -MethodName GetSTRINGvalue -Arguments @{
                    hDefKey=[uint32]2147483650; sSubKeyName='SOFTWARE\Microsoft\Windows NT\CurrentVersion'; sValueName='ReleaseId'}

    $PSVersion = Invoke-CimMethod @Params -Namespace root\cimv2 -ClassName StdRegProv -MethodName GetSTRINGvalue -Arguments @{
                    hDefKey=[uint32]2147483650; sSubKeyName='SOFTWARE\Microsoft\PowerShell\3\PowerShellEngine'; sValueName='PowerShellVersion'}

    foreach ($OS in $OSInfo) {
        if (-not $PSBoundParameters.CimSession) {
            $OSVersion.PSComputerName = $OS.CSName
            $PSVersion.PSComputerName = $OS.CSName
        }
        
        $PS = $PSVersion | Where-Object PSComputerName -eq $OS.CSName
                    
        if (-not $PS.sValue) {
            $Params2 = @{}
            
            if ($PSBoundParameters.CimSession) {
                $Params2.CimSession = $CimSession | Where-Object ComputerName -eq $OS.CSName
            }

            $PS = Invoke-CimMethod @Params2 -Namespace root\cimv2 -ClassName StdRegProv -MethodName GetSTRINGvalue -Arguments @{
                        hDefKey=[uint32]2147483650; sSubKeyName='SOFTWARE\Microsoft\PowerShell\1\PowerShellEngine'; sValueName='PowerShellVersion'}
        }
            
        [pscustomobject]@{
            ComputerName = $OS.CSName
            OperatingSystem = $OS.Caption
            Version = ($OSVersion | Where-Object PSComputerName -eq $OS.CSName).sValue
            BuildNumber = $OS.BuildNumber
            OSArchitecture = $OS.OSArchitecture
            PowerShellVersion = $PS.sValue
                                        
        }
            
    }

}</pre>
One of the most difficult parts of writing this function was figuring out how to query the registry of a remote computer with a CIM session. I also didn't want to retrieve the information from one remote computer at a time. Instead, I wanted to retrieve the information for all of the computers at once and then put the pieces together before returning the information. While this is a little more complicated than query one computer at a time, the added efficiency and reduced run-time makes it worth the effort.

Simply run the function with no parameters to retrieve information from the local computer.
<pre class="lang:ps decode:true">Get-MrOSInfo</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo1a.jpg"><img class="alignnone size-full wp-image-15957" src="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo1a.jpg" alt="" width="859" height="188" /></a>

Now, I'll create CIM sessions to several different servers running different operating system versions and various versions of PowerShell.
<pre class="lang:ps decode:true ">$CimSession = New-MrCimSession -ComputerName dc01, sql05, sql08, sql14, srv1, srv2
$CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo2b.jpg"><img class="alignnone size-full wp-image-15961" src="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo2b.jpg" alt="" width="859" height="549" /></a>

Notice that by using my <em>New-MrCimSession</em> function, it automatically created all of the ones capable of using the WSMan protocol using it and the one that wasn't using the DCom protocol. The SQL05 server runs Windows Server 2008 (non-R2) and does not have PowerShell installed at all.
<pre class="lang:ps decode:true">Get-MrOSInfo -CimSession $CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo3a.jpg"><img class="alignnone size-full wp-image-15959" src="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo3a.jpg" alt="" width="859" height="608" /></a>

The version number such as 1511, 1607, 1703, or 1709 will only show up for machines running Windows 10 or Server 2016 (or higher if you're reading this in the future), but the neat thing is the <em>Get-MrOSInfo</em> function is able to query down level servers such as SQL05 that do not have PowerShell installed.

I went ahead and added PowerShell version 1.0 to my SQL05 server. When the same command is run again, it shows that server does indeed now have PowerShell 1.0 installed. That particular version of PowerShell does not include remoting or the ability to return the version with the <em>$PSVersionTable</em> built-in variable.
<pre class="lang:ps decode:true">Get-MrOSInfo -CimSession $CimSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo4a.jpg"><img class="alignnone size-full wp-image-15963" src="http://mikefrobbins.com/wp-content/uploads/2017/12/osinfo4a.jpg" alt="" width="859" height="609" /></a>

Both the <em>Get-MrOSInfo</em> and <em>New-MrCimSession</em> functions shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

µ