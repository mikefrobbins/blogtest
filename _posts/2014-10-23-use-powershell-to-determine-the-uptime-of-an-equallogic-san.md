---
ID: 10723
post_title: >
  Use PowerShell to Determine the Uptime
  of an EqualLogic SAN
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/10/23/use-powershell-to-determine-the-uptime-of-an-equallogic-san/
published: true
post_date: 2014-10-23 07:30:03
---
Recently, I received notification from EqualLogic support that there could be issues with their storage area networks running firmware version 7.0.x at 248 consecutive days of uninterrupted operation and they recommend updating to firmware version 7.0.9 to correct (and prevent) the problem.

While I know that I have EqualLogic storage area networks running in some of the data-centers I support, I'm not sure what version of firmware they're running or what the uptime on them is so I'll use PowerShell (of course) to determine this information.

The EqlPSTools PowerShell module is part of the EqualLogic HIT (Host Integration Tools) Kit for Microsoft which can be downloaded from the <a href="https://eqlsupport.dell.com/secure/login.aspx" target="_blank">EqualLogic support site</a>. Note that the support site states that customers must have an active service plan to gain access to it.

I decided to write a reusable function to perform the necessary tasks since the EqlPSTools module is in a DLL file that has to be manually imported and you would normally have to connect to each SAN, query it and them disconnect from it moving on to the next one.

I didn't want to have to figure all of that out each time I needed to determine this information so I decided to write a function that would do all of that for me. I could even add it to a script module that exists in my <em>$env:PSModulePath</em> so it would automatically be available any time I needed it without having to find and dot source a ps1 file.
<pre class="lang:ps decode:true " title="Get-MrEqlSANUptime">#Requires -Version 3.0
function Get-MrEqlSANUptime {

&lt;#
.SYNOPSIS
    Gets the uptime for one or more EqualLogic storage area network members.
 
.DESCRIPTION
    Get-MrEqlSANUptime is a PowerShell function that retrieves the firmware
    version, last boot time, and number of days of uptime from one or more
    EqualLogic storage area network members. This function depends on the
    EqlPSTools PowerShell module that is installed as part of the EqualLogic
    HIT Kit. 
 
.PARAMETER GroupAddress
    One or more IP Addresses of the EqualLogic storage area network(s) to
    return the uptime information for.
 
.PARAMETER ModulePath
    Full path to the EqlPSTools.dll file that is installed as part of the
    EqualLogic HIT Kit. This parameter defaults to the default location of
    this DLL file.

.PARAMETER Credential
    Specifies a user account that has permission to perform this action. The
    default is the current user.
 
.EXAMPLE
     Get-MrEqlSANUptime -GroupAddress 192.168.1.1 -Credential (Get-Credential)
 
.EXAMPLE
     Get-MrEqlSANUptime -GroupAddress 192.168.1.1 -ModulePath 'C:\Program Files\EqualLogic\bin\EqlPSTools.dll'
 
.EXAMPLE
     '192.168.1.1', '192.168.2.1' | Get-MrEqlSANUptime -Credential (Get-Credential)
 
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
        [Parameter(Mandatory,
                   ValueFromPipeline)]
        [string[]]$GroupAddress,
        
        [ValidateNotNullOrEmpty()]
        [string]$ModulePath = 'C:\Program Files\EqualLogic\bin\EqlPSTools.dll',
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
        $problem = $false

        $Params = @{
            ErrorAction = 'Stop'
        }

        Write-Verbose -Message "Attempting to load EqlPSTools Module if it's not already loaded"
        if (-not (Get-Module -Name EqlPSTools)) {          
            try {
                Import-Module $ModulePath @Params
            }
            catch {
                $problem = $true
                Write-Warning -Message "An error has occured.  Error details: $_.Exception.Message"
            }
        }
 
        If ($PSBoundParameters['Credential']) {
            $Params.credential = $Credential
        }
    }

    PROCESS {
        if (-not ($problem)) {
            foreach ($Group in $GroupAddress) {
                $Params.GroupAddress = $Group

                try {
                    $Connect = Connect-EqlGroup @Params
                    Write-Verbose -Message "$Connect"
                }
                catch {
                    $Problem = $True
                    Write-Warning -Message "Please contact your system administrator. Reference error: $_.Exception.Message"
                }

                if (-not($Problem)) {
                    Get-EqlMember -GroupAddress $Group |
                    Select-Object -Property MemberName,
                                            @{label='FirmwareVersion';expression={$_.FirmwareVersion -replace '^.*V'}},
                                            @{label='BootTime';expression={(Get-Date).AddSeconds(-$_.Uptime)}},
                                            @{label='UpTime(Days)';expression={'{0:N2}' -f ($_.UpTime / 86400) }}

                    $Disconnect = Disconnect-EqlGroup -GroupAddress $Group
                    Write-Verbose -Message "$Disconnect"
                }

                $Problem = $false
            }
        }
    }
}</pre>
The <em>Get-EqlMember</em> cmdlet which is part of the EqlPSTools PowerShell module returns an uptime property, but it's in total seconds and I'm not sure about you but a number like 12801888 doesn't mean much to me without performing some additional calculations on it and I wanted the function to do all of that for me (automate all things):
<pre class="lang:ps decode:true  ">Get-MrEqlSANUptime -GroupAddress 192.168.1.1, 192.168.2.1 -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/san-uptime1.png"><img class="alignnone size-full wp-image-10724" src="http://mikefrobbins.com/wp-content/uploads/2014/10/san-uptime1.png" alt="san-uptime1" width="877" height="203" /></a>

The <em>Get-EqlMember</em> cmdlet only accepts a single IP address via parameter input and it can't be provided via pipeline input so I would have had to query the SAN in each data-center manually, one at a time or write some code to put them into a <a href="http://technet.microsoft.com/en-us/library/hh849731.aspx" target="_blank"><em>ForEach-Object</em></a> loop.

I wrote my reusable function so it would not only allow multiple IP addresses to be provided via parameter input, but also accept them via pipeline input in case I wanted to query the actual IP addresses from somewhere else like a text file or CSV and then provide that output as input for my function:
<pre class="lang:ps decode:true">$cred= Get-Credential
'192.168.1.1', '192.168.2.1', '192.168.3.1' | Get-MrEqlSANUptime -Credential $cred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/san-uptime2.png"><img class="alignnone size-full wp-image-10725" src="http://mikefrobbins.com/wp-content/uploads/2014/10/san-uptime2.png" alt="san-uptime2" width="877" height="227" /></a>

Based on the previous results, the reported issue isn't a crisis at the moment, but it's something that needs to be taken care of in less than 100 days and I can see there are a couple more that need to have their firmware updated that are on older versions.

The <i>Get-EqlSANUptime</i> PowerShell function shown in this blog article can be downloaded from the <a href="https://gallery.technet.microsoft.com/scriptcenter/Use-PowerShell-to-49653c9e" target="_blank">TechNet Script Center Repository</a> or from <a href="http://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell Repository on GitHub</a>.

µ