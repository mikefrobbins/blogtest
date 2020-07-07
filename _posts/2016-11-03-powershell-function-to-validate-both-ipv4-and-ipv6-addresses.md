---
ID: 14671
post_title: >
  PowerShell Function to Validate Both
  IPv4 and IPv6 Addresses
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/11/03/powershell-function-to-validate-both-ipv4-and-ipv6-addresses/
published: true
post_date: 2016-11-03 07:30:17
---
Last week, I wrote a blog article about <a href="http://mikefrobbins.com/2016/10/27/powershell-ip-address-type-accelerator-not-100-accurate/" target="_blank">the IP address type accelerator in PowerShell not being 100% accurate</a> and I demonstrated using it for parameter validation. If you want to simply validate an IP Address and return a Boolean, that's a little more complicated so I decided to write a function to perform that task along with providing detailed information about the IP address when an optional detailed parameter is specified:
<pre class="lang:ps decode:true" title="Test-MrIpAddress">#Requires -Version 2.0
function Test-MrIpAddress {

&lt;#
.SYNOPSIS
    Tests one or more IP Addresses to determine if they are valid.
 
.DESCRIPTION
    Test-MrIpAddress is an advanced function that tests one or more IP Addresses to determine if
    they are valid. The detailed parameter can be used to return additional information about the IP.
 
.PARAMETER IpAddress
    One or more IP Addresses to test. This parameter is mandatory.

.PARAMETER Detailed
    Switch parameter to return detailed infomation about the IP Address instead of a boolean.
 
.EXAMPLE
     Test-MrIpAddress -IpAddress '192.168.0.1', '192.168.0.256'

.EXAMPLE
     Test-MrIpAddress -IpAddress '192.168.0.1' -Detailed

.EXAMPLE
     '::1', '192.168.0.256' | Test-MrIpAddress

.INPUTS
    String
 
.OUTPUTS
    Boolean
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true,
                   ValueFromPipeLine=$true)]
        [string[]]$IpAddress,

        [switch]$Detailed
    )

    PROCESS {

        foreach ($Ip in $IpAddress) {
    
            try {
                $Results = $Ip -match ($DetailedInfo = [IPAddress]$Ip)
            }
            catch {
                Write-Output $false
                Continue
            }

            if (-not($PSBoundParameters.Detailed)){
                Write-Output $Results
            }
            else {
                Write-Output $DetailedInfo
            }    
    
        }

    }

}</pre>
The IpAddress parameter can accept one or more IP addresses. By default a Boolean is returned but specifying the detailed parameter returns more information if a valid IP address is specified. IP addresses are also accepted via pipeline input:
<pre class="lang:ps decode:true">Test-MrIpAddress -IpAddress '192.168.0.1', '192.168.0.256'
Test-MrIpAddress -IpAddress '192.168.0.1' -Detailed
'::1', '192.168.0.256' | Test-MrIpAddress</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-5a.png"><img class="alignnone size-full wp-image-14667" src="http://mikefrobbins.com/wp-content/uploads/2016/10/testip-5a.png" alt="testip-5a" width="859" height="297" /></a>

The <em>Test-MrIpAddress</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ