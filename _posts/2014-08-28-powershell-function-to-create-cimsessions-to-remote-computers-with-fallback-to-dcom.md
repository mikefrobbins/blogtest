---
ID: 10321
post_title: >
  PowerShell Function to Create
  CimSessions to Remote Computers with
  Fallback to Dcom
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/08/28/powershell-function-to-create-cimsessions-to-remote-computers-with-fallback-to-dcom/
published: true
post_date: 2014-08-28 07:30:25
---
I've found myself writing the same code over and over in my PowerShell scripts where I'm performing a task that requires me to create a CimSession to one or more computers without knowing what version of PowerShell is installed on the remote computers or if PowerShell is even installed on the remote computers.

While it is possible to create a CimSession to a remote computer in any of those scenarios I just described, it's a fair amount of code to add to multiple scripts and then the same redundant code ends up being in multiple files which creates a nightmare if you ever find an issue with the code and need to update it for any reason.

I decided to turn the code I would use to create these CimSessions into a function which is shown below and add it to a script module so I could just call the function from the PowerShell script that needed the CimSessions created before the script performs its task(s).
<pre class="lang:ps decode:true " title="New-MrCimSession">#Requires -Version 3.0
function New-MrCimSession {
&lt;#
.SYNOPSIS
    Creates CimSessions to remote computer(s), automatically determining if the WSMAN
    or Dcom protocol should be used.
.DESCRIPTION
    New-MrCimSession is a function that is designed to create CimSessions to one or more
    computers, automatically determining if the default WSMAN protocol or the backwards
    compatible Dcom protocol should be used. PowerShell version 3 is required on the
    computer that this function is being run on, but PowerShell does not need to be
    installed at all on the remote computer.
.PARAMETER ComputerName
    The name of the remote computer(s). This parameter accepts pipeline input. The local
    computer is the default.
.PARAMETER Credential
    Specifies a user account that has permission to perform this action. The default is
    the current user.
.EXAMPLE
     New-MrCimSession -ComputerName Server01, Server02
.EXAMPLE
     New-MrCimSession -ComputerName Server01, Server02 -Credential (Get-Credential)
.EXAMPLE
     Get-Content -Path C:\Servers.txt | New-MrCimSession
.INPUTS
    String
.OUTPUTS
    Microsoft.Management.Infrastructure.CimSession
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipeline)]
        [ValidateNotNullorEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,
 
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )

    BEGIN {
        $Opt = New-CimSessionOption -Protocol Dcom

        $SessionParams = @{
            ErrorAction = 'Stop'
        }

        If ($PSBoundParameters['Credential']) {
            $SessionParams.Credential = $Credential
        }
    }

    PROCESS {
        foreach ($Computer in $ComputerName) {
            $SessionParams.ComputerName  = $Computer

            if ((Test-WSMan -ComputerName $Computer -ErrorAction SilentlyContinue).productversion -match 'Stack: ([3-9]|[1-9][0-9]+)\.[0-9]+') {
                try {
                    Write-Verbose -Message "Attempting to connect to $Computer using the WSMAN protocol."
                    New-CimSession @SessionParams
                }
                catch {
                    Write-Warning -Message "Unable to connect to $Computer using the WSMAN protocol. Verify your credentials and try again."
                }
            }
 
            else {
                $SessionParams.SessionOption = $Opt

                try {
                    Write-Verbose -Message "Attempting to connect to $Computer using the DCOM protocol."
                    New-CimSession @SessionParams
                }
                catch {
                    Write-Warning -Message "Unable to connect to $Computer using the WSMAN or DCOM protocol. Verify $Computer is online and try again."
                }

                $SessionParams.Remove('SessionOption')
            }            
        }
    }
}</pre>
This function will create a CimSession to a remote computer(s) using the WSMAN protocol if the stack version is 3.0 or higher:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession3.png"><img class="alignnone size-full wp-image-10325" src="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession3.png" alt="new-mrcimsession3" width="877" height="168" /></a>

The following regular expression matches any version that is 3.0 or higher:
<pre class="lang:ps decode:true ">([3-9]|[1-9][0-9]+)\.[0-9]+</pre>
<span style="color: #3366ff;">[3-9]</span> Numbers three through nine.
<span style="color: #3366ff;">|</span> or
<span style="color: #3366ff;">[1-9]</span> Numbers one through nine. <span style="color: #3366ff;">[0-9]</span> Followed by numbers zero through nine. <span style="color: #3366ff;">+</span> One or more times.
<span style="color: #3366ff;">\.</span> A "dot" is a special character so it must be escaped with a backslash.
<span style="color: #3366ff;">[0-9]+</span>  Numbers zero through nine. One or more times.

I'll use this function to create a CimSession to servers named SQL01 and SQL03:
<pre class="lang:ps decode:true">New-MrCimSession -ComputerName SQL01, SQL03</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession1.png"><img class="alignnone size-full wp-image-10322" src="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession1.png" alt="new-mrcimsession1" width="877" height="252" /></a>

Notice in the previous example that one CimSession was created with the WSMAN protocol and the other one with DCOM. Believe it or not, SQL03 is a Windows 2008 Server (non-R2) that does NOT have PowerShell installed at all:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession2.png"><img class="alignnone size-full wp-image-10323" src="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession2.png" alt="new-mrcimsession2" width="800" height="600" /></a>

The previously created CimSessions can be used to query information from WMI on the remote computers regardless of which underlying protocol is used for communication:
<pre class="lang:ps decode:true ">Get-CimInstance -CimSession (Get-CimSession) -ClassName Win32_OperatingSystem |
Select-Object -Property PSComputerName, Caption</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession4.png"><img class="alignnone size-full wp-image-10332" src="http://mikefrobbins.com/wp-content/uploads/2014/08/new-mrcimsession4.png" alt="new-mrcimsession4" width="877" height="170" /></a>

The function shown in the blog article can also be <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-a7fb6800" target="_blank">downloaded</a> from the TechNet Script Repository.

Update: The most recent version can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ