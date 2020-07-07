---
ID: 15624
post_title: >
  Configure Internet Connection Sharing
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/10/19/configure-internet-connection-sharing-with-powershell/
published: true
post_date: 2017-10-19 07:30:03
---
My test environment runs as Hyper-V VM's on my IBM Thinkpad P50. I use ICS (Internet Connection Sharing) to shield my Hyper-V test environment virtual network from my production network connection. ICS allows me to temporarily give my test VM's Internet access when needed without putting them directly on the production network. You might ask why? Because my test environment VM's run things like DHCP servers that would cause havoc on the production network. I use an Internal virtual switch with Hyper-V and I keep both that internal network adapter and ICS disabled unless I need the VM's to have Internet access.

From what I've found, there's no easy way to configure ICS with PowerShell so I decided to write a couple of functions to accomplish that task. The first function, <em>Get-MrInternetConnectionSharing</em> retrieves the current settings.
<pre class="lang:ps decode:true" title="Get-MrInternetConnectionSharing">#Requires -Version 3.0
function Get-MrInternetConnectionSharing {

&lt;#
.SYNOPSIS
    Retrieves the status of Internet connection sharing for the specified network adapter(s).
 
.DESCRIPTION
    Get-MrInternetConnectionSharing is an advanced function that retrieves the status of Internet connection sharing
    for the specified network adapter(s).
 
.PARAMETER InternetInterfaceName
    The name of the network adapter(s) to check the Internet connection sharing status for.
 
.EXAMPLE
    Get-MrInternetConnectionSharing -InternetInterfaceName Ethernet, 'Internal Virtual Switch'

.EXAMPLE
    'Ethernet', 'Internal Virtual Switch' | Get-MrInternetConnectionSharing

.EXAMPLE
    Get-NetAdapter | Get-MrInternetConnectionSharing

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
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [Alias('Name')]
        [string[]]$InternetInterfaceName
    )

    BEGIN {
        regsvr32.exe /s hnetcfg.dll
        $netShare = New-Object -ComObject HNetCfg.HNetShare
    }

    PROCESS {
        foreach ($Interface in $InternetInterfaceName){
        
            $publicConnection = $netShare.EnumEveryConnection |
            Where-Object {
                $netShare.NetConnectionProps.Invoke($_).Name -eq $Interface
            }
            
            try {
                $Results = $netShare.INetSharingConfigurationForINetConnection.Invoke($publicConnection)
            }
            catch {
                Write-Warning -Message "An unexpected error has occurred for network adapter: '$Interface'"
                Continue
            }

            [pscustomobject]@{
                Name = $Interface
                SharingEnabled = $Results.SharingEnabled
                SharingConnectionType = $Results.SharingConnectionType
                InternetFirewallEnabled = $Results.InternetFirewallEnabled
            }
            
        }
    
    }    

}</pre>
Since the function used to retrieve the information can accept more than one network adapter name, I didn't want to use parameter validation to verify the name otherwise the function would terminate for all the ones specified if any invalid names were specified. I decided to handle any invalid network adapter names in the code itself with a try/catch block and a continue statement which skips producing any output for the invalid ones other than the warning message that I have it generate with <em>Write-Warning</em>.

I'll specify the two network adapters used in my Internet connection scenario.
<pre class="lang:ps decode:true">Get-MrInternetConnectionSharing -InternetInterfaceName Ethernet, 'Internal Virtual Switch'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/ics1a.jpg"><img class="alignnone size-full wp-image-15625" src="http://mikefrobbins.com/wp-content/uploads/2017/09/ics1a.jpg" alt="" width="859" height="141" /></a>

The second function, <em>Set-MrInternetConnectionSharing</em> configures the settings.
<pre class="lang:ps decode:true " title="Set-MrInternetConnectionSharing">#Requires -Version 3.0 -Modules NetAdapter
function Set-MrInternetConnectionSharing {

&lt;#
.SYNOPSIS
    Configures Internet connection sharing for the specified network adapter(s).
 
.DESCRIPTION
    Set-MrInternetConnectionSharing is an advanced function that configures Internet connection sharing
    for the specified network adapter(s). The specified network adapter(s) must exist and must be enabled.
    To enable Internet connection sharing, Internet connection sharing cannot already be enabled on any
    network adapters.
 
.PARAMETER InternetInterfaceName
    The name of the network adapter to enable or disable Internet connection sharing for.
 
 .PARAMETER LocalInterfaceName
    The name of the network adapter to share the Internet connection with.

 .PARAMETER Enabled
    Boolean value to specify whether to enable or disable Internet connection sharing.

.EXAMPLE
    Set-MrInternetConnectionSharing -InternetInterfaceName Ethernet -LocalInterfaceName 'Internal Virtual Switch' -Enabled $true

.EXAMPLE
    'Ethernet' | Set-MrInternetConnectionSharing -LocalInterfaceName 'Internal Virtual Switch' -Enabled $false

.EXAMPLE
    Get-NetAdapter -Name Ethernet | Set-MrInternetConnectionSharing -LocalInterfaceName 'Internal Virtual Switch' -Enabled $true

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
                   ValueFromPipeline,
                   ValueFromPipelineByPropertyName)]
        [ValidateScript({
            If ((Get-NetAdapter -Name $_ -ErrorAction SilentlyContinue -OutVariable INetNIC) -and (($INetNIC).Status -ne 'Disabled' -or ($INetNIC).Status -ne 'Not Present')) {
                $True
            }
            else {
                Throw "$_ is either not a valid network adapter of it's currently disabled."
            }
        })]
        [Alias('Name')]
        [string]$InternetInterfaceName,

        [ValidateScript({
            If ((Get-NetAdapter -Name $_ -ErrorAction SilentlyContinue -OutVariable LocalNIC) -and (($LocalNIC).Status -ne 'Disabled' -or ($INetNIC).Status -ne 'Not Present')) {
                $True
            }
            else {
                Throw "$_ is either not a valid network adapter of it's currently disabled."
            }
        })]
        [string]$LocalInterfaceName,

        [Parameter(Mandatory)]
        [bool]$Enabled
    )

    BEGIN {
        if ((Get-NetAdapter | Get-MrInternetConnectionSharing).SharingEnabled -contains $true -and $Enabled) {
            Write-Warning -Message 'Unable to continue due to Internet connection sharing already being enabled for one or more network adapters.'
            Break
        }

        regsvr32.exe /s hnetcfg.dll
        $netShare = New-Object -ComObject HNetCfg.HNetShare
    }
    
    PROCESS {
        
        $publicConnection = $netShare.EnumEveryConnection |
        Where-Object {
            $netShare.NetConnectionProps.Invoke($_).Name -eq $InternetInterfaceName
        }

        $publicConfig = $netShare.INetSharingConfigurationForINetConnection.Invoke($publicConnection)

        if ($PSBoundParameters.LocalInterfaceName) {
            $privateConnection = $netShare.EnumEveryConnection |
            Where-Object {
                $netShare.NetConnectionProps.Invoke($_).Name -eq $LocalInterfaceName
            }

            $privateConfig = $netShare.INetSharingConfigurationForINetConnection.Invoke($privateConnection)
        } 
        
        if ($Enabled) {
            $publicConfig.EnableSharing(0)
            if ($PSBoundParameters.LocalInterfaceName) {
                $privateConfig.EnableSharing(1)
            }
        }
        else {
            $publicConfig.DisableSharing()
            if ($PSBoundParameters.LocalInterfaceName) {
                $privateConfig.DisableSharing()
            }
        }

    }

}</pre>
I've handled all of the problems that I ran into when testing this function. The specified network adapters have to exist as well as be enabled in order to be configured otherwise an error is generated. Both of those problems are handled early on with input validation via ValidateScript since there's no need to allow the function to even get started unless those requirements are met.

Next, I ran into a scenario where ICS was disabled for the Internet connection network adapter, but was enabled for the Internal one which generated an error. I've handled that problem first thing in the Begin block by checking to make sure ICS is disabled before attempting to continue.

Now I'll configure the network adapter named Ethernet to share its Internet connection with the one named Internal Virtual Switch.
<pre class="lang:ps decode:true">Set-MrInternetConnectionSharing -InternetInterfaceName Ethernet -LocalInterfaceName 'Internal Virtual Switch' -Enabled $true</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/ics2a.jpg"><img class="alignnone size-full wp-image-15626" src="http://mikefrobbins.com/wp-content/uploads/2017/09/ics2a.jpg" alt="" width="859" height="67" /></a>

You can see that Internet connection is enabled by checking the settings again.
<pre class="lang:ps decode:true ">Get-MrInternetConnectionSharing -InternetInterfaceName Ethernet, 'Internal Virtual Switch'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/09/ics3a.jpg"><img class="alignnone size-full wp-image-15627" src="http://mikefrobbins.com/wp-content/uploads/2017/09/ics3a.jpg" alt="" width="859" height="141" /></a>

The functions shown in this blog article can be downloaded as part of my MrToolkit module from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

µ