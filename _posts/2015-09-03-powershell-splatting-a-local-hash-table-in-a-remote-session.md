---
ID: 12358
post_title: '#PowerShell: Splatting a Local Hash Table in a Remote Session'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/09/03/powershell-splatting-a-local-hash-table-in-a-remote-session/
published: true
post_date: 2015-09-03 07:30:18
---
I'm sure that most people who use PowerShell on a regular basis are familiar with the "<em>Using</em>" variable scope modifier which allows for the use of local variables in a remote session. This functionality was introduced with PowerShell version 3:
<pre class="lang:ps decode:true">$MyVar = 'Bits', 'W32time'

Invoke-Command -ComputerName DC01 {
    Get-Service -Name $Using:MyVar
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/using-splatting1a.jpg"><img class="alignnone size-full wp-image-12359" src="http://mikefrobbins.com/wp-content/uploads/2015/09/using-splatting1a.jpg" alt="using-splatting1a" width="877" height="191" /></a>

A fairly simple concept, right? But wait a minute, did you know that you can also define a local hash table to be used with splatting in a remote session using this same concept?
<pre class="lang:ps decode:true ">$MyParams = @{
    ClassName = 'Win32_Service'
    Filter = "Name = 'bits' or Name = 'W32time'"
}

Invoke-Command -ComputerName DC01 {
    Get-CimInstance @Using:MyParams
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/09/using-splatting2a.jpg"><img class="alignnone size-full wp-image-12360" src="http://mikefrobbins.com/wp-content/uploads/2015/09/using-splatting2a.jpg" alt="using-splatting2a" width="877" height="240" /></a>

The previous example is a simple one, but this concept comes in handy when writing an advanced function where you've wrapped your function around PowerShell remoting like in the following example where I use a hash table from the local session to control the <em>PassThru</em>, <em>WhatIf</em>, and <em>Confirm</em> parameters that are taking place in the remote session:
<pre class="lang:ps decode:true " title="Start-MrAutoStoppedService">#Requires -Version 3.0
function Start-MrAutoStoppedService {
    
&lt;#
.SYNOPSIS
    Starts services that are set to start automatically, are not currently running,
    excluding the services that are set to delayed start.
 
.DESCRIPTION
    Start-MrAutoStoppedService is a function that starts services on the specified
    remote computer(s) that are set to start automatically, are not currently running,
    and it excludes the services that are set to start automatically with a delayed
    startup. This function requires PowerShell version 3 or higher on the machine it
    is being run from, PowerShell version 2 or higher on the remote machine that it is
    being run against, and PowerShell remoting to be enabled on the remote computer.
 
.PARAMETER ComputerName
    The remote computer(s) to check the status and start the services on.

.PARAMETER Credential
    Specifies a user account that has permission to perform this action. The default
    is the current user.

.PARAMETER PassThru
    Returns an object representing the service. By default, this function does not
    generate any output.
     
.EXAMPLE
     Start-MrAutoStoppedService -ComputerName 'Server1', 'Server2'

.EXAMPLE
     Start-MrAutoStoppedService -ComputerName 'Server1', 'Server2' -PassThru

.EXAMPLE
     'Server1', 'Server2' | Start-MrAutoStoppedService

.EXAMPLE
     Start-MrAutoStoppedService -ComputerName 'Server1', 'Server2' -Credential (Get-Credential)
      
.INPUTS
    String
 
.OUTPUTS
    None or ServiceController
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding(SupportsShouldProcess)]
    param (
        [Parameter(Mandatory,
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [string[]]$ComputerName,

        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty,

        [switch]$PassThru
    )

    BEGIN {
        $Params = @{}
        $RemoteParams = @{}

        switch ($PSBoundParameters) {
            {$_.keys -contains 'Credential'} {$Params.Credential = $Credential}
            {$_.keys -contains 'PassThru'} {$RemoteParams.PassThru = $true}
            {$_.keys -contains 'Confirm'} {$RemoteParams.Confirm = $true}
            {$_.keys -contains 'WhatIf'} {$RemoteParams.WhatIf = $true}
        }

    }

    PROCESS {
        $Params.ComputerName = $ComputerName

        Invoke-Command @Params {            
            $Services = Get-WmiObject -Class Win32_Service -Filter {
                State != 'Running' and StartMode = 'Auto'
            }
            
            foreach ($Service in $Services.Name) {
                Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\$Service" |
                Where-Object {$_.Start -eq 2 -and $_.DelayedAutoStart -ne 1} |
                Select-Object -Property @{label='ServiceName';expression={$_.PSChildName}} |
                Start-Service @Using:RemoteParams
            }            
        }
    }
}</pre>
The <em>Start-MrAutoStoppedService</em> function shown in the previous code example can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ