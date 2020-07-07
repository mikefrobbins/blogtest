---
ID: 9947
post_title: 'If (‘Interested&#8217; -in &#8216;PowerShell&#8217;) {&#8216;Are you Participating in the PowerShell.org Forums?&#8217;}'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/05/if-interested-in-powershell-are-you-participating-in-the-powershell-org-forums/
published: true
post_date: 2014-06-05 07:30:41
---
If you're interested in PowerShell and you're not participating in <a href="http://powershell.org/wp/forums/" target="_blank">the forums at PowerShell.org</a>, you're missing out on an awesome opportunity to learn from others instead of having to re-invent the wheel each time you need to accomplish a task. If you've been using PowerShell for a while and you're not participating in the forums, then you're missing out on an opportunity to help others who are asking for your assistance.

Recently, <a href="http://powershell.org/wp/forums/topic/get-ip-address-and-fqdn-from-netbios-name/" target="_blank">a topic was posted in the forums</a> about retrieving the fully qualified domain name and IP address information from a DNS server with PowerShell.

Here's the solution I provided:
<pre class="lang:ps decode:true" title="Get-MrDNSInfo">#Requires -Version 3.0
#Requires -Modules DNSClient
function Get-MrDNSInfo {

&lt;#
.SYNOPSIS
    Returns the FQDN and IP Address information for one or more computers. 
 
.DESCRIPTION
    Get-MrDNSInfo is a function that returns the Fully Quailified Domain Name,
    along with IPv4 and IPv6 information for one or more computers which is
    retrieved from a Microsoft based DNS server.
 
.PARAMETER ComputerName
    The computer(s) to retrieve FQDN and IP Address information for. The default is
    the local computer.
 
.PARAMETER DNSServer
    The Microsoft based DNS Server to retrieve the FQDN and IP Address information
    from. The default is a DNS Server named DC01.
 
.EXAMPLE
    Get-MrDNSInfo -ComputerName pc01, pc02, server01
 
.EXAMPLE
     Get-MrDNSInfo -ComputerName pc01, pc02, server01 -DNSServer MyDNSServer
 
.EXAMPLE
     'pc01', 'pc02', 'server01' | Get-MrDNSInfo
 
.EXAMPLE
    Get-Content -Path .\Computers.txt | Get-MrDNSInfo -DNSServer MyDNSServer
 
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
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName = $env:COMPUTERNAME,

        [ValidateNotNullOrEmpty()]
        [string]$DNSServer = 'dc01'

    )

    PROCESS {
        foreach ($Computer in $ComputerName) {
            $DNSInfo = Resolve-DnsName -Server $DNSServer -Name $Computer -ErrorAction SilentlyContinue

            [PSCustomObject]@{    
                NetBIOSName = $Computer
                FQDN = $DNSInfo.Name | Get-Unique
                IP4Address = $DNSInfo.IP4Address
                IPV6Address = $DNSInfo.IP6Address
            }

        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/05/052914-1.jpg"><img class="alignnone size-full wp-image-9948" src="http://mikefrobbins.com/wp-content/uploads/2014/05/052914-1.jpg" alt="052914-1" width="877" height="216" /></a>

Where else could you receive this type of information for free? A nicely formatted, well documented, and easy to read and follow solution.

By the way, I'm speaking on "<a href="http://www.atltechstravaganza.com/presentation/whats-new-in-powershell-version-5-preview/" target="_blank">What's New in PowerShell Version 5 (Preview)?</a>" at <a href="http://www.atltechstravaganza.com/" target="_blank">TechStravaganza in Atlanta</a> on Friday, June 6th (tomorrow).

µ