---
ID: 15211
post_title: >
  PowerShell Function to Determine
  PSSessions to your Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/05/04/powershell-function-to-determine-pssessions-to-your-servers/
published: true
post_date: 2017-05-04 07:30:07
---
This past week, I needed to determine if anyone had a PSSession connected to any of the servers that I support. This is fairly easy to accomplish with a PowerShell one-liner, but I can never remember the syntax so I decided to create a reusable function to accomplish the task.
<pre class="lang:ps decode:true " title="Get-MrRemotePSSession">#Requires -Version 3.0
function Get-MrRemotePSSession {

&lt;#
.SYNOPSIS
    Retrieves a list of the Windows PowerShell sessions that are connected to the specified remote computer(s).
 
.DESCRIPTION
    The Get-MrRemotePSSession function gets the user-managed Windows PowerShell sessions (PSSessions) on remote
    computers even if they were not created in the current session.
 
.PARAMETER ComputerName
    Specifies an array of names of computers. Gets the sessions that connect to the specified computers.
    Wildcard characters are not permitted. The default value is the local computer.

.PARAMETER Credential
    Specifies a user credential. This function runs the command with the permissions of the specified user.
    Specify a user account that has permission to connect to the remote computer. The default is the current
    user. Type a user name, such as `User01`, `Domain01\User01`, or `User@Domain.com`, or enter a PSCredential
    object, such as one returned by the Get-Credential cmdlet. When you type a user name, this cmdlet prompts
    you for a password.
 
.EXAMPLE
     Get-MrRemotePSSession -ComputerName Server01, Server02 -Credential (Get-Credential)

.EXAMPLE
     'Server01', 'Server02' | Get-MrRemotePSSession -Credential (Get-Credential)

.INPUTS
    String
 
.OUTPUTS
    PSCustomObject
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline)]
        [string[]]$ComputerName = $env:COMPUTERNAME ,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
        $Params = @{
            ResourceURI = 'shell'
            Enumerate = $true
        }

        if ($PSBoundParameters.Credential) {
            $Params.Credential = $Credential
        }
    }

    PROCESS {
        foreach ($Computer in $ComputerName) {
            $Params.ConnectionURI = "http://$($Computer):5985/wsman"

            Get-WSManInstance @Params |
            Select-Object -Property @{label='PSComputerName';expression={$Computer}}, Name, Owner, ClientIP, State        
        }
    }

}</pre>
The function accepts multiple computer names via parameter input:
<pre class="lang:ps decode:true "> Get-MrRemotePSSession -ComputerName dc01, sql02 | Format-Table</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/05/remote-pssession1a.png"><img class="alignnone size-full wp-image-15212" src="http://mikefrobbins.com/wp-content/uploads/2017/05/remote-pssession1a.png" alt="" width="859" height="167" /></a>

It also accepts the computer names via pipeline input and it has a Credential parameter so that alternate credentials can be specified:
<pre class="lang:ps decode:true ">'dc01', 'sql02' | Get-MrRemotePSSession -Credential (Get-Credential) | Format-Table</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/05/remote-pssession2a.png"><img class="alignnone size-full wp-image-15213" src="http://mikefrobbins.com/wp-content/uploads/2017/05/remote-pssession2a.png" alt="" width="859" height="216" /></a>

The <em>Get-MrRemotePSSession</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener noreferrer">my PowerShell repository on GitHub</a>.

µ