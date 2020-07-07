---
ID: 15935
post_title: >
  Determine the Start Time of a Windows
  Service with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/11/30/determine-the-start-time-of-a-windows-service-with-powershell/
published: true
post_date: 2017-11-30 07:30:49
---
Recently, I was asked to setup a scheduled task or job to restart specific services on certain servers each night and while that's simple, how do you know for sure the services were indeed restarted? One way is to determine how long a service has been running or when they were started. The dilemma is the necessary information isn't available using the <em>Get-Service</em> cmdlet or with CIM or WMI using the <em>Get-CimInstance</em> or <em>Get-WmiObject</em> cmdlets with the <em>Win32_Service</em> class.

The good news is that every Windows service that's running has an underlying process and the start time of a process can be determined with either the <em>Get-Process</em> cmdlet or the WMI <em>Win32_Process</em> class. All you have to do us match up the services to the corresponding process.

<em>Get-Service</em> doesn't include the process id so I had to resort to using WMI to retrieve the service information so the process id can be retrieved. A PowerShell one-liner can be used to retrieve the information I was looking for.
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_Service -PipelineVariable Service |
ForEach-Object {
    Get-Process -Id $_.ProcessId |
    Select-Object -Property @{label='Status';expression={$Service.State}},
                            @{label='Name';expression={$Service.Name}},
                            @{label='DisplayName';expression={$Service.DisplayName}},
                            StartTime
}</pre>
That one-liner could simply be run inside of <em>Invoke-Command</em> to retrieve the same information from remote systems, but I decided to turn it into a function instead. My first iteration was simple:
<pre class="lang:ps decode:true ">function Get-MrService {
    [CmdletBinding()]
    param (
        [string]$Name
    )

    $Params = @{}

    if ($PSBoundParameters.Name) {
        $Params.Filter = "Name = '$Name'"
    }

    $Services = Get-CimInstance -ClassName Win32_Service @Params
    
    foreach ($Service in $Services) {
        $Process = Get-Process -Id $Service.ProcessId
    
        [pscustomobject]@{
            Status = $Service.State
            Name = $Service.Name
            DisplayName = $Service.DisplayName
            StartTime = $Process.StartTime
        }
    }

}</pre>
When adding remoting within the function itself, I ran into a problem where the services were retrieved from the remote system, but it was trying to match them up to local processes so a little rework was required.

In the end, I decided to use CIM sessions for remote connectivity so the part of the function which previously used the <em>Get-Process</em> cmdlet was changed to use <em>Get-CimInstance</em> with the <em>Win32_Process</em> class.
<pre class="lang:ps decode:true">#Requires -Version 3.0
function Get-MrService {

&lt;#
.SYNOPSIS
    Gets the services on a local or remote computer.
 
.DESCRIPTION
    The Get-MrService function gets objects that represent the services on a local computer or on a remote computer,
    including running and stopped services. You can direct this function to get only particular services by specifying
    the service name of the services.
 
.PARAMETER Name
    Specifies the service names of services to be retrieved. Wildcards are permitted. By default, this function gets
    all of the services on the computer.
 
 .PARAMETER CimSession
    Specifies the CIM session to use for this cmdlet. Enter a variable that contains the CIM session or a command that
    creates or gets the CIM session, such as the New-CimSession or Get-CimSession cmdlets. For more information, see
    about_CimSessions.

.EXAMPLE
    Get-MrService -Name bits, w32time

.EXAMPLE
    Get-MrService -CimSession (New-CimSession -ComputerName Server01, Server02) -Name Win*

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
        [ValidateNotNullOrEmpty()]        
        [string[]]$Name = '*',

        [Microsoft.Management.Infrastructure.CimSession[]]$CimSession
    )

    $ServiceParams = @{}

    if ($PSBoundParameters.CimSession) {
        $ServiceParams.CimSession = $CimSession
    }    

    foreach ($n in $Name) {
        if ($n -match '\*') {
            $n = $n -replace '\*', '%'
        }
        
        $Services = Get-CimInstance -ClassName Win32_Service -Filter "Name like '$n'" @ServiceParams
        
        foreach ($Service in $Services) {

            if ($Service.ProcessId -ne 0) {
                $ProcessParams = @{}

                if ($PSBoundParameters.CimSession) {
                    $ProcessParams.CimSession = $CimSession | Where-Object ComputerName -eq $Service.SystemName
                }

                $Process = Get-CimInstance -ClassName Win32_Process -Filter "ProcessId = '$($Service.ProcessId)'" @ProcessParams
            }
            else {
                $Process = ''
            }
    
            [pscustomobject]@{
                ComputerName = $Service.SystemName
                Status = $Service.State
                Name = $Service.Name
                DisplayName = $Service.DisplayName
                StartTime = $Process.CreationDate
            }

        }

    }
    
}</pre>
I also ran into a problem where all of the systems were being queried for the process id of every service instead of just the ones running on that particular system. I narrowed down which CIM sessions were being queried which resolved that problem.
<pre class="lang:ps decode:true ">Get-MrService -Name msdtc, bits, w32time, winrm |
Format-Table -AutoSize

$CimSession = New-MrCimSession -ComputerName dc01, sql08, sql14

Get-MrService -CimSession $CimSession -Name msdtc, bits, w32time, winrm |
Sort-Object -Property ComputerName, Name |
Format-Table -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/11/service-starttime1a.jpg"><img class="alignnone size-full wp-image-15942" src="http://mikefrobbins.com/wp-content/uploads/2017/11/service-starttime1a.jpg" alt="" width="859" height="442" /></a>

Although this function requires PowerShell 3.0 or higher on the system it's being run from, since I chose to use CIM sessions for remote connectivity instead of simply wrapping the commands inside of PowerShell remoting, I'm able to target machines using either WSMan or DCom. For more information on that topic, see my "<a href="http://mikefrobbins.com/2012/09/20/targeting-down-level-clients-with-the-get-ciminstance-powershell-cmdlet/" target="_blank" rel="noopener">Targeting Down Level Clients with the Get-CimInstance PowerShell Cmdlet</a>" blog article.

The previous example takes advantage of my <em>New-MrCimSession</em> function which automatically determines if a system is able to use WSMan otherwise it automatically falls back to using DCom. More information about that function can be found in my "<a href="http://mikefrobbins.com/2014/08/28/powershell-function-to-create-cimsessions-to-remote-computers-with-fallback-to-dcom/" target="_blank" rel="noopener">PowerShell Function to Create CimSessions to Remote Computers with Fallback to Dcom</a>" blog article.

The <em>Get-MrService</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

µ