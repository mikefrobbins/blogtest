---
ID: 9024
post_title: >
  Using PowerShell to Investigate
  Duplicate IP Address Issues that were
  issued from a Microsoft based DHCP
  Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/02/06/using-powershell-to-investigate-duplicate-ip-address-issues-that-were-issued-from-a-microsoft-based-dhcp-server/
published: true
post_date: 2014-02-06 07:30:55
---
Over the past couple of weeks, you've received several support calls about duplicate IP Address issues from users whose machines receive their IP Address from your organizations internal Microsoft based DHCP Servers.

The users who reported the issues use thin clients and when the IP Address conflicts occur they receive a popup message containing their IP Address and the MAC Address of the machine that duplicated the IP Address.

You and your staff need to retrieve additional information about the IP Address and MAC Address that the users received in order to try to determine what's causing the problem.

While this information could be obtained from the DHCP Servers using PowerShell one-liners, you've decided to write a function to simplify the process of obtaining this sort of information long term for you and for your staff.
<pre class="lang:ps decode:true" title="Get-IPDuplicateInfo">#Requires -Version 4.0
#Requires -Modules DhcpServer

function Get-IPDuplicateInfo {

&lt;#
.SYNOPSIS
    Retrieves the DHCP Lease Info for a specific IP Address or MAC Address from a Windows based DHCP Server.

.DESCRIPTION
    Get-IPDuplicateInfo is a function that retrieves information about a specific IP address or MAC Address from
a Microsoft Windows Server that is running the DHCP Server role.    

.PARAMETER IPAddress
    The IP Address that your attempting to retrieve DHCP information for. This parameter is mandatory and should
be entered in the following format: 192.168.1.1

.PARAMETER ClientId
    The Media Access Control (MAC) Address of the network card that you're attempting to retrieve information for.
The parameter is mandatory and the value for it must be specified in MAC-48 format which is six groups of two
hexadecimal digits separated by dashes: 01-23-45-67-89-AB.

.EXAMPLE
    Get-IPDuplicateInfo -IPAddress '192.168.1.1'

.EXAMPLE
    Get-IPDuplicateInfo -ClientId '01-23-45-67-89-AB'

.INPUTS
    None

.OUTPUTS
    DhcpServerv4Lease

.NOTES
    Created On: February 6, 2014
    Created By: Mike F Robbins
    Website: http://mikefrobbins.com/
    Twitter: @mikefrobbins
    Contact Email: IgBtAGkAawBlAGYAcgBvAGIAYgBpAG4AcwBAAG0AcwBwAHMAdQBnAC4AYwBvAG0AIgA=

#&gt;

    [CmdletBinding(DefaultParameterSetName='IP Address')]
    param(
        [Parameter(Mandatory,
                   ParameterSetName='IP Address')]
        [ValidateScript({
            If ($_ -match "^(([01]?[0-9]{1,2}|2[0-4][0-9]|25[0-5])\.){3}([01]?[0-9]{1,2}|2[0-4][0-9]|25[0-5])$") {
                $True
            }
            else {
                Throw "$_ is not a valid IPv4 Address or was not specified in the required format. Enter a valid IP address in the following format: '192.168.1.1'"
            }
        })]
        [string]$IPAddress,

        [Parameter(Mandatory,
                   ParameterSetName='MAC Address')]
        [ValidateScript({
            If ($_ -match '^([0-9A-F]{2}[-]){5}([0-9A-F]{2})$') {
                $True
            }
            else {
                Throw "$_ is either not a valid MAC Address or was not specified in the required format. Please specify one or more MAC addresses in the follwing format: '01-23-45-67-89-AB'"
            }
        })]
        [Alias('MACAddress')]
        [string]$ClientId,

        [Parameter(ParameterSetName='IP Address')]
        [Parameter(ParameterSetName='MAC Address')]
        [Parameter(DontShow)]
        [ValidateNotNullOrEmpty()]
        [Alias('ServerName')] 
        [string[]]$ComputerName = (Get-DhcpServerInDC |
                                   Select-Object -ExpandProperty DnsName)
    )

    foreach ($computer in $ComputerName){

        $Params = @{
            ComputerName = $computer
            ErrorAction = 'Stop'
        }

        If ($PSBoundParameters['IPAddress']) {
            $Params.IPAddress = $IPAddress

            try {
                Get-DhcpServerv4Lease @Params
            }
            catch [Microsoft.Management.Infrastructure.CimException]{
                Write-Verbose -Message "No DHCP Lease exists on DHCP Server $computer for IPAddress '$IPAddress'."
            }
            catch {
                Write-Warning -Message "An error has occured. Error Details: $_.Exception.Message"
            }

        }

        elseif ($PSBoundParameters['ClientId']) {

            try {
                $DHCPScopes = (Get-DhcpServerv4Scope @Params |
                               Select-Object -ExpandProperty ScopeId).IPAddressToString
            }
            catch {
                Write-Warning -Message "Unable to obtain DHCP Scope information from DHCP Server $computer. Error Details: '$_.Exception.Message'." 
            }

            $Params.ClientId = $ClientId

            foreach ($DHCPScope in $DHCPScopes) { 
                $Params.ScopeId = $DHCPScope

                try {
                    Get-DhcpServerv4Lease @Params
                }
                catch [Microsoft.Management.Infrastructure.CimException]{
                    Write-Verbose -Message "Lease information for Client ID '$ClientId' not found in DHCP Scope $DHCPScope on DHCP Server $ComputerName." 
                }
                catch {
                    Write-Warning -Message "An error has occured. Error Details: $_.Exception.Message"
                }
            }

        }

        else {        
            Write-Error -Message "An error has occurred. Please contact your system administrator."                
        }

    }

}</pre>
Simply provide an IP Address or MAC Address and information about it is returned from the DHCP Server(s):

<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/duplicate-ip1.png"><img class="alignnone size-full wp-image-9026" alt="duplicate-ip1" src="http://mikefrobbins.com/wp-content/uploads/2014/02/duplicate-ip1.png" width="877" height="217" /></a>

To receive even more detailed information, pipe the results to the Select-Object cmdlet and select all of the properties:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/duplicate-ip2.png"><img class="alignnone size-full wp-image-9028" alt="duplicate-ip2" src="http://mikefrobbins.com/wp-content/uploads/2014/02/duplicate-ip2.png" width="877" height="348" /></a>

This function was tested from a Windows 8.1 workstation with the Remote Server Administration Tools (RSAT) installed and it was tested against a Windows Server 2012 R2 server and a Windows Server 2008 R2 server, both with the DHCP Server role installed.

This PowerShell function can also be downloaded from the TechNet <a href="http://gallery.technet.microsoft.com/scriptcenter/Investigate-Duplicate-IP-c71ff6e9" target="_blank">script repository</a>.

µ