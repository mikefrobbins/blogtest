---
ID: 12312
post_title: 'My Solution: August 2015 PowerShell Scripting Games Puzzle'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/30/my-solution-august-2015-powershell-scripting-games-puzzle/
published: true
post_date: 2015-08-30 12:00:38
---
A couple of months ago, <a href="http://powershell.org/" target="_blank" rel="noopener">PowerShell.org</a> announced that the PowerShell Scripting Games had been re-imagined as a monthly puzzle. In August, <a href="http://powershell.org/wp/2015/08/01/august-2015-scripting-games-puzzle/" target="_blank" rel="noopener">the second puzzle</a> was published.

The instructions stated that a one-liner could be used if you were using a newer version of PowerShell. A public JSON endpoint can be found at http://www.telize.com/geoip and your goal is to write some PowerShell code to display output similar to the following:
<pre class="lang:ps decode:true">longitude latitude continent_code timezone
--------- -------- -------------- --------
-115.1685 36.2212  NA             America/Los_Angeles</pre>
Try to accomplish this with a one-liner but use full cmdlet and parameter names. Write an advanced function that's a wrapper for this endpoint.

Here's my one-liner solution. It requires PowerShell version 3 or higher:
<pre class="lang:ps decode:true ">Invoke-RestMethod -Uri 'www.telize.com/geoip' |
Select-Object -Property longitude, latitude, continent_code, timezone</pre>
I decided to write a reusable tool and create a script module out of it named "MrGeo" that I could add additional Geolocation related functions to in the future:
<pre class="lang:ps decode:true" title="MrGeo.psm1">#Requires -Version 3.0
function Get-MrGeoInformation {

&lt;#
.SYNOPSIS
    Queries www.telize.com for Geolocation information based on IP Address.
 
.DESCRIPTION
    Get-MrGeoInformation is a PowerShell function that is designed to query
    www.telize.com for Geolocation information for one or more IPv4 or IPv6 IP
    Addresses. If an IP Address is not specified, your public IP Address is used.
 
.PARAMETER IPAddress
    The IPAddress(es) to return the Geolocation information for.
 
.EXAMPLE
     Get-MrGeoInformation
 
.EXAMPLE
     Get-MrGeoInformation -IPAddress '46.19.37.108', '2a02:2770::21a:4aff:feb3:2ee'
 
.EXAMPLE
     '46.19.37.108', '2a02:2770::21a:4aff:feb3:2ee' | Get-MrGeoInformation
 
.INPUTS
    IPAddress
 
.OUTPUTS
    GeoInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline)]
        [ipaddress[]]$IPAddress
    )

    PROCESS { 

        if (-not($PSBoundParameters.IPAddress)) {
            Write-Verbose -Message 'Attempting to retrieve Geolocation information for your public IP Address'
            $Results = Invoke-RestMethod -Uri 'http://www.telize.com/geoip' -TimeoutSec 30
        }
        else {
            $Results = foreach ($IP in $IPAddress) {
                Write-Verbose -Message "Attempting to retrieving Geolocation information for IP Address: '$IP'"
                Invoke-RestMethod -Uri "http://www.telize.com/geoip/$IP" -TimeoutSec 30
            }
        }

        foreach ($Result in $Results) {
            $Result.PSTypeNames.Insert(0,'Mr.GeoInfo')
            Write-Output $Result
        }       

    }

}</pre>
Notice that in the previous code I added my initials (Mr) as a prefix for the noun to help prevent name collisions with other people's functions that are named the same thing. I also added additional functionality so that in addition to retrieving the information for your current public IP address, that one or more public IPv4 or IPv6 addresses could be specified. Comment based help has been included along with verbose output. Pipeline input is accepted for the IPAddress parameter and the [ipaddress] type accelerator is used to perform parameter validation for both IPv4 and IPv6 addresses for that parameter.

I created custom formating for the module to display the required output plus the IP address by default for both table and list output but additional data can retrieved by simply piping to <em>Select-Object</em>, <em>Format-Table,</em> or <em>Format-List</em> and specifying -Property * or specific properties without having to modify the function itself.
<pre class="lang:ps decode:true" title="MrGeo.ps1xml">&lt;?xml version="1.0" encoding="utf-8" ?&gt;
&lt;Configuration&gt;
    &lt;ViewDefinitions&gt;
        &lt;View&gt;
            &lt;Name&gt;Mr.GeoInfo&lt;/Name&gt;
            &lt;ViewSelectedBy&gt;
                &lt;TypeName&gt;Mr.GeoInfo&lt;/TypeName&gt;
            &lt;/ViewSelectedBy&gt;
            &lt;TableControl&gt;
                &lt;TableHeaders&gt;
                     &lt;TableColumnHeader&gt;
                        &lt;Width&gt;30&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;                   
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;11&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;10&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;16&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                    &lt;TableColumnHeader&gt;
                        &lt;Width&gt;20&lt;/Width&gt;
                    &lt;/TableColumnHeader&gt;
                &lt;/TableHeaders&gt;
                &lt;TableRowEntries&gt;
                    &lt;TableRowEntry&gt;
                        &lt;TableColumnItems&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;ip&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;longitude&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;latitude&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;continent_code&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                            &lt;TableColumnItem&gt;
                                &lt;PropertyName&gt;timezone&lt;/PropertyName&gt;
                            &lt;/TableColumnItem&gt;
                        &lt;/TableColumnItems&gt;
                    &lt;/TableRowEntry&gt;
                 &lt;/TableRowEntries&gt;
            &lt;/TableControl&gt;
        &lt;/View&gt;
        &lt;View&gt;
            &lt;Name&gt;Mr.GeoInfo&lt;/Name&gt;
            &lt;ViewSelectedBy&gt;
                &lt;TypeName&gt;Mr.GeoInfo&lt;/TypeName&gt;
            &lt;/ViewSelectedBy&gt;
            &lt;ListControl&gt;
                &lt;ListEntries&gt;
                    &lt;ListEntry&gt;
                        &lt;ListItems&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;ip&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;longitude&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;latitude&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;continent_code&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                            &lt;ListItem&gt;
                                &lt;PropertyName&gt;timezone&lt;/PropertyName&gt;
                            &lt;/ListItem&gt;
                        &lt;/ListItems&gt;
                    &lt;/ListEntry&gt;
                &lt;/ListEntries&gt;
            &lt;/ListControl&gt;
        &lt;/View&gt;
    &lt;/ViewDefinitions&gt;
&lt;/Configuration&gt;</pre>
As a best practice, always create a module manifest when creating a PowerShell module:
<pre class="lang:ps decode:true" title="MrGeo.psd1">#
# Module manifest for module 'MrGeo'
#
# Generated by: Mike F Robbins
#
# Generated on: 8/8/2015
#

@{

# Script module or binary module file associated with this manifest.
RootModule = 'MrGeo'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '942fa453-a5c2-4bbd-9e6e-a2783bdb48e8'

# Author of this module
Author = 'Mike F Robbins'

# Company or vendor of this module
CompanyName = 'mikefrobbins.com'

# Copyright statement for this module
Copyright = '(c) 2015 Mike F Robbins. All rights reserved.'

# Description of the functionality provided by this module
Description = 'Mike F Robbins Geo PowerShell Module'

# Minimum version of the Windows PowerShell engine required by this module
PowerShellVersion = '3.0'

# Name of the Windows PowerShell host required by this module
# PowerShellHostName = ''

# Minimum version of the Windows PowerShell host required by this module
# PowerShellHostVersion = ''

# Minimum version of Microsoft .NET Framework required by this module
# DotNetFrameworkVersion = ''

# Minimum version of the common language runtime (CLR) required by this module
# CLRVersion = ''

# Processor architecture (None, X86, Amd64) required by this module
# ProcessorArchitecture = ''

# Modules that must be imported into the global environment prior to importing this module
# RequiredModules = @()

# Assemblies that must be loaded prior to importing this module
# RequiredAssemblies = @()

# Script files (.ps1) that are run in the caller's environment prior to importing this module.
# ScriptsToProcess = @()

# Type files (.ps1xml) to be loaded when importing this module
# TypesToProcess = @()

# Format files (.ps1xml) to be loaded when importing this module
FormatsToProcess = 'MrGeo.ps1xml'

# Modules to import as nested modules of the module specified in RootModule/ModuleToProcess
# NestedModules = @()

# Functions to export from this module
FunctionsToExport = '*'

# Cmdlets to export from this module
CmdletsToExport = '*'

# Variables to export from this module
VariablesToExport = '*'

# Aliases to export from this module
AliasesToExport = '*'

# DSC resources to export from this module
# DscResourcesToExport = @()

# List of all modules packaged with this module
# ModuleList = @()

# List of all files packaged with this module
# FileList = @()

# Private data to pass to the module specified in RootModule/ModuleToProcess. This may also contain a PSData hashtable with additional module metadata used by PowerShell.
PrivateData = @{

    PSData = @{

        # Tags applied to this module. These help with module discovery in online galleries.
        # Tags = @()

        # A URL to the license for this module.
        # LicenseUri = ''

        # A URL to the main website for this project.
        # ProjectUri = ''

        # A URL to an icon representing this module.
        # IconUri = ''

        # ReleaseNotes of this module
        # ReleaseNotes = ''

    } # End of PSData hashtable

} # End of PrivateData hashtable

# HelpInfo URI of this module
# HelpInfoURI = ''

# Default prefix for commands exported from this module. Override the default prefix using Import-Module -Prefix.
# DefaultCommandPrefix = ''

}</pre>
Notice that PowerShell version 3 is specified in the manifest as the minimum version required by this module. The custom format ps1xml file is also specified in the manifest. If you plan to publish your module in a NuGet repository with <a href="http://mikefrobbins.com/2015/04/23/powershellget-the-big-easy-way-to-discover-install-and-update-powershell-modules/" target="_blank" rel="noopener">PowerShellGet</a> that ships in PowerShell version 5, you'll need to specify an author and description in the manifest.

Give it a try with both IPv4 and IPv6 addresses and both pipeline and parameter input:
<pre class="lang:ps decode:true ">'46.19.37.108', '2a02:2770::21a:4aff:feb3:2ee' | Get-MrGeoInformation
Get-MrGeoInformation -IPAddress '46.19.37.108', '2a02:2770::21a:4aff:feb3:2ee'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/mr-geo1a.jpg"><img class="alignnone size-full wp-image-12314" src="http://mikefrobbins.com/wp-content/uploads/2015/08/mr-geo1a.jpg" alt="mr-geo1a" width="859" height="238" /></a>

One of the reasons I prefer to place functions like this in a PowerShell script module is that with PowerShell version 3 and higher, you can simply call the function and the module will auto-load as long as it exists in the <em>$env:PSModulePath</em>. No need to remember or figure out where you saved that ps1 file that contains the function and no need to dot source it.

The MrGeo PowerShell script module shown in this blog article can be downloaded from my <a href="https://github.com/mikefrobbins/ScriptingGames" target="_blank" rel="noopener">Scripting Games repository on GitHub</a>.

Also, be sure to see the official <a href="http://powershell.org/wp/2015/08/30/2015-august-scripting-games-wrap-up/" target="_blank" rel="noopener">2015-August Scripting Games Wrap-Up</a> article on PowerShell.org for more information and solutions for the August scripting games puzzle.

<hr />

Update February 20th, 2019
The public API this functions uses has been shutdown. I've updated the function to use a different API and moved it to <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell repository on GitHub</a>.

µ