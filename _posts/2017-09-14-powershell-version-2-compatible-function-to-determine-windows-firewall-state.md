---
ID: 15575
post_title: >
  PowerShell Version 2 Compatible Function
  to Determine Windows Firewall State
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/09/14/powershell-version-2-compatible-function-to-determine-windows-firewall-state/
published: true
post_date: 2017-09-14 07:30:30
---
I recently had a need to perform some security auditing on an environment that still has some servers running PowerShell version 2 (<a href="https://blogs.msdn.microsoft.com/powershell/2017/08/24/windows-powershell-2-0-deprecation/" target="_blank" rel="noopener">PowerShell version 2 is deprecated</a>). One of the things I needed to determine was whether or not the Windows firewall was enabled on each of the servers in the environment. Luckily, all of the servers at least had PowerShell remoting enabled.
<pre class="lang:ps decode:true " title="Get-MrNetFirewallState">#Requires -Version 2.0
function Get-MrNetFirewallState {

&lt;#
.SYNOPSIS
    Displays the per-profile state of the Windows Firewall.
 
.DESCRIPTION
    The Get-MrNetFirewallState function displays the current firewall state for the Domain, Private, and Public profiles.
 
.PARAMETER ComputerName
    The name of the computer(s) to retrieve the firewall state for.

.PARAMETER Credential
    Specifies a user account that has permission to perform this action. The default is the current user.
 
.EXAMPLE
     Get-MrNetFirewallState

.EXAMPLE
     Get-MrNetFirewallState -ComputerName Server01, Server02

.EXAMPLE
     Get-MrNetFirewallState -ComputerName Server01, Server02 -Credential (Get-Credential)
 
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
        [string[]]$ComputerName = $env:COMPUTERNAME,
        
        [System.Management.Automation.Credential()]$Credential = [System.Management.Automation.PSCredential]::Empty
    )
    
    $ScriptBlock =
@'
    $Results = netsh.exe advfirewall show allprofiles | Select-String -SimpleMatch Profile, State
    
    for ($i = 0; $i -lt 6; $i += 2) {
        New-Object PSObject -Property @{
            ComputerName = $env:COMPUTERNAME
            Name = ($Results[$i] | Select-String -SimpleMatch 'Profile Settings') -replace '^*.Profile Settings:'
            Enabled = if ($Results[$i+1] -match 'ON') {
                          $true
                      }
                      else {
                          $false
                      }
        }
    }
'@
    
    $Params = @{        
        Scriptblock = [Scriptblock]::Create($ScriptBlock)
    }
    
    if ($ComputerName -ne $env:COMPUTERNAME) {
        
        $Params.ComputerName = $ComputerName
        $Params.ErrorAction = 'SilentlyContinue'
        $Params.ErrorVariable = 'Problem'
        
        if ($PSBoundParameters.Credential) {
            $Params.Credential = $Credential
        }
            
        Invoke-Command @Params | Select-Object -Property ComputerName, Name, Enabled
        
        foreach ($p in $Problem) {
            if ($p.FullyQualifiedErrorId -match 'AccessDenied|LogonFailure') {
                Write-Warning -Message "Access Denied when trying to connect to $($p.TargetObject)" 
            }
            elseif ($p.FullyQualifiedErrorId -match 'NetworkPathNotFound') {
                Write-Warning -Message "Unable to connect to $($p.targetobject)"
            }
            else {
                Write-Warning -Message "An unexpected error has occurred when trying to connect to $($p.targetobject)"
            }
        }
        
    }
    else {
        $Params.ScriptBlock.Invoke()
    }

}</pre>
The PowerShell function shown in the previous code example is a wrapper for the <em>Netsh.exe</em> command. The commands that are going to be executed are stored in a here-string and then converted to a script block within the parameters hash table. It uses <em>Invoke-Command</em> to execute <em>Netsh</em> on the remote servers and them uses <em>Select-String</em> to parse down the data to only the lines that are necessary. The results are turned into a usable custom object in a <em>For</em> loop that uses <em>Select-String</em> again to parse out some of the unnecessary information from each of the remaining lines. If the credential parameter is specified, it's also added to the parameters hash table. Splatting is used with <em>Invoke-Command.</em>

Custom error handling has been added to allow the function to take advantage of running against multiple computers at the same time via <em>Invoke-Command</em> while providing the user with feedback when errors occur such as being unable to connect to the remote computer.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper4a.jpg"><img class="alignnone size-full wp-image-15588" src="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper4a.jpg" alt="" width="859" height="201" /></a>

I found that two different errors (access denied and logon failure) can be returned which both mean access denied. During the last few months, I've performed a technical technical review on the "<em>Windows Server 2016 Automation with PowerShell Cookbook</em>". Based on reviewing <a href="https://twitter.com/doctordns" target="_blank" rel="noopener">Thomas Lee</a>'s PowerShell code, I learned that the <em>Match</em> operator can be used similarly to the <em>Like</em> operator except without the need to specify wildcard characters. I had previously only used it when I needed to specify a regular expression.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper5a.jpg"><img class="alignnone size-full wp-image-15589" src="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper5a.jpg" alt="" width="859" height="93" /></a>

If you're going to use the <em>GroupBy</em> parameter of <em>Format-Table</em>, you have to sort by that property first.
<pre class="lang:ps decode:true">Get-MrNetFirewallState -ComputerName FS1, FS2, SRV1, SRV2 |
Sort-Object -Property ComputerName |
Format-Table -GroupBy ComputerName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper1a.jpg"><img class="alignnone size-full wp-image-15579" src="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper1a.jpg" alt="" width="859" height="537" /></a>

If the function is run against the local computer, the script block is simply invoked. While this doesn't cover all scenarios where the local computer could be specified, it's a great option for testing against a local computer where PowerShell remoting isn't enabled.
<pre class="lang:ps decode:true">Get-MrNetFirewallState</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper2a.jpg"><img class="alignnone size-full wp-image-15580" src="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper2a.jpg" alt="" width="859" height="155" /></a>

This task is much easier to accomplish with newer versions of PowerShell using the <em>Get-NetFirewallProfile</em> cmdlet which was introduced in PowerShell version 3.0.
<pre class="lang:ps decode:true">Get-NetFirewallProfile -CimSession fs1, fs2, srv1, srv2 |
Sort-Object -Property PSComputerName |
Format-Table -Property PSComputerName, Name, Enabled -GroupBy PSComputerName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper3a.jpg"><img class="alignnone size-full wp-image-15582" src="http://mikefrobbins.com/wp-content/uploads/2017/09/netsh-firewall-wrapper3a.jpg" alt="" width="859" height="538" /></a>

Here's a bonus tip: Even though commands such as <em>Get-NetFirewallProfile</em> don't have a <em>ComputerName</em> parameter, a computer name can be specified with the <em>CimSession</em> parameter without needing to first create a CIM session. A CIM session will automatically be created behind the scenes using the WSMan protocol and then it will be automatically removed once the command completes (a special thanks to <a href="https://twitter.com/RSiddaway" target="_blank" rel="noopener">Richard Siddaway</a> for clarifying this for me a few weeks ago).

The <em>Get-MrNetFirewallState</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

µ