---
ID: 10500
post_title: >
  Creating a Custom DSC Resource to
  Configure Jumbo Frames on Network Cards
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/10/02/creating-a-custom-dsc-resource-to-configure-jumbo-frames-on-network-cards/
published: true
post_date: 2014-10-02 07:30:59
---
Recently while working on configuring a new server with DSC (Desired State Configuration), I discovered that there wasn't a DSC resource for configuring the jumbo frames setting on network cards so I decided to write my own.

This blog article steps you through the process of creating a custom DSC resource, specifically one that is used to configure the jumbo frames setting on network cards. The specific settings for jumbo frames can vary depending on your network card manufacturer and the specific driver. I believe that I've narrowed down the settings so this resource should work on all network cards/drivers that support jumbo frames. I've seen some drivers that don't support jumbo frames in the past, but I'm yet to find one to test this resource on so I can add additional code to check to see if jumbo frames is even supported.

<span style="color: #ff0000;"><strong>Consider this DSC resource to be beta and use it at your own risk &lt;period&gt;.</strong></span>

The workstation used in this scenario is running Windows 8.1 and the server is running Windows Server 2012 R2 and they are both a member of the same domain. Both are running PowerShell version 4 which ships in the box with both of those operating systems.

First, download the <a href="http://gallery.technet.microsoft.com/scriptcenter/xDscResourceDesigne-Module-22eddb29" target="_blank">xDscResourceDesigner PowerShell Module</a> from the Microsoft Script Repository and while you're at it, why not use PowerShell to perform the actual download:
<pre class="lang:ps decode:true">Invoke-WebRequest -Uri 'http://gallery.technet.microsoft.com/scriptcenter/xDscResourceDesigne-Module-22eddb29/file/120244/1/xDSCResourceDesigner_1.1.1.zip' -OutFile 'C:\tmp\xDSCResourceDesigner_1.1.1.zip' -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource1.jpg"><img class="alignnone size-full wp-image-10506" src="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource1.jpg" alt="dsc-resource1" width="877" height="120" /></a>

Since the zip file was downloaded from the Internet, you'll need to unblock it:
<pre class="lang:ps decode:true">Unblock-File -Path 'C:\tmp\xDSCResourceDesigner_1.1.1.zip' -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource2.jpg"><img class="alignnone size-full wp-image-10507" src="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource2.jpg" alt="dsc-resource2" width="877" height="72" /></a>

As specified in the instructions, extract the contents of the downloaded file to a folder under "$env:ProgramFiles\WindowsPowerShell\Modules".

Since the machine I'm using is running Windows 8.1 with PowerShell version 4 installed, I'll use a function I created a while back to accomplish the task of extracting the contents of the zip file to the specified folder:
<pre class="lang:ps decode:true ">Unzip-File -File 'C:\tmp\xDSCResourceDesigner_1.1.1.zip' -Destination "$env:ProgramFiles\WindowsPowershell\Modules" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource3.jpg"><img class="alignnone size-full wp-image-10508" src="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource3.jpg" alt="dsc-resource3" width="877" height="96" /></a>

My <em>Unzip-File</em> function can be downloaded from the <a href="http://gallery.technet.microsoft.com/scriptcenter/PowerShell-Function-to-727d6200" target="_blank">Microsoft Script Repository</a>.

If you happen to be running the <a href="http://blogs.msdn.com/b/powershell/archive/2014/09/04/windows-management-framework-5-0-preview-september-2014-is-now-available.aspx" target="_blank">Windows Management Framework 5.0 Preview September 2014</a> version, you can use the native <em>Extract-Archive</em> cmdlet instead:
<pre class="lang:ps decode:true">Expand-Archive -Path 'C:\tmp\xDSCResourceDesigner_1.1.1.zip' -DestinationPath "$env:ProgramFiles\WindowsPowershell\Modules" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource4.jpg"><img class="alignnone size-full wp-image-10509" src="http://mikefrobbins.com/wp-content/uploads/2014/09/dsc-resource4.jpg" alt="dsc-resource4" width="877" height="119" /></a>

Now to create what I like to call the skeleton of your DSC resource:
<pre class="lang:ps decode:true">$InterfaceAlias = New-xDscResourceProperty –Name InterfaceAlias –Type String –Attribute Key
$Ensure = New-xDscResourceProperty –Name Ensure –Type String –Attribute Write –ValidateSet 'Absent', 'Present'
New-xDscResource –Name cJumboFrames -Property $InterfaceAlias, $Ensure -Path "$env:ProgramFiles\WindowsPowershell\Modules\cJumboFrames"</pre>
If you receive a strange error like this one:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource5.jpg"><img class="alignnone size-full wp-image-10511" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource5.jpg" alt="dsc-resource5" width="877" height="155" /></a>

Check the execution policy on the machine you're working on. Any setting other than "Restricted" should work. My recommendation is "RemoteSigned":
<pre class="lang:ps decode:true">Get-ExecutionPolicy
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Force
Get-ExecutionPolicy</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource6.jpg"><img class="alignnone size-full wp-image-10513" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource6.jpg" alt="dsc-resource6" width="877" height="109" /></a>

Let's try that again now that the execution policy has been set to remote signed:
<pre class="lang:ps decode:true">$InterfaceAlias = New-xDscResourceProperty –Name InterfaceAlias –Type String –Attribute Key
$Ensure = New-xDscResourceProperty –Name Ensure –Type String –Attribute Write –ValidateSet 'Absent', 'Present'
New-xDscResource –Name cJumboFrames -Property $InterfaceAlias, $Ensure -Path "$env:ProgramFiles\WindowsPowershell\Modules\cJumboFrames"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource7.jpg"><img class="alignnone size-full wp-image-10515" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource7.jpg" alt="dsc-resource7" width="877" height="422" /></a>

Notice in the previous command, I added a prefix of "c" to the name of the JumboFrames DSC resource that I'm creating. That stands for "community".

Creating the "skeleton" could also be accomplished with a PowerShell one-liner. This time I'll also add the <em>-Verbose</em> parameter so you can see the details of what's happening:
<pre class="lang:ps decode:true">New-xDscResource –Name cJumboFrames -Property (
    New-xDscResourceProperty –Name InterfaceAlias –Type String –Attribute Key), (
    New-xDscResourceProperty –Name Ensure –Type String –Attribute Write –ValidateSet 'Absent', 'Present'
) -Path "$env:ProgramFiles\WindowsPowershell\Modules\cJumboFrames" -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource8.jpg"><img class="alignnone size-full wp-image-10516" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource8.jpg" alt="dsc-resource8" width="877" height="739" /></a>

That creates the directory structure, a PowerShell script module (psm1 file):
<pre class="lang:ps decode:true" title="cJumboFrames.psm1">function Get-TargetResource
{
	[CmdletBinding()]
	[OutputType([System.Collections.Hashtable])]
	param
	(
		[parameter(Mandatory = $true)]
		[System.String]
		$InterfaceAlias
	)

	#Write-Verbose "Use this cmdlet to deliver information about command processing."

	#Write-Debug "Use this cmdlet to write debug information while troubleshooting."


	&lt;#
	$returnValue = @{
		InterfaceAlias = [System.String]
		Ensure = [System.String]
	}

	$returnValue
	#&gt;
}


function Set-TargetResource
{
	[CmdletBinding()]
	param
	(
		[parameter(Mandatory = $true)]
		[System.String]
		$InterfaceAlias,

		[ValidateSet("Absent","Present")]
		[System.String]
		$Ensure
	)

	#Write-Verbose "Use this cmdlet to deliver information about command processing."

	#Write-Debug "Use this cmdlet to write debug information while troubleshooting."

	#Include this line if the resource requires a system reboot.
	#$global:DSCMachineStatus = 1


}


function Test-TargetResource
{
	[CmdletBinding()]
	[OutputType([System.Boolean])]
	param
	(
		[parameter(Mandatory = $true)]
		[System.String]
		$InterfaceAlias,

		[ValidateSet("Absent","Present")]
		[System.String]
		$Ensure
	)

	#Write-Verbose "Use this cmdlet to deliver information about command processing."

	#Write-Debug "Use this cmdlet to write debug information while troubleshooting."


	&lt;#
	$result = [System.Boolean]
	
	$result
	#&gt;
}


Export-ModuleMember -Function *-TargetResource</pre>
And a mof schema file:
<pre class="lang:ps decode:true" title="cJumboFrames.schema.mof">[ClassVersion("1.0.0.0"), FriendlyName("cJumboFrames")]
class cJumboFrames : OMI_BaseResource
{
	[Key] String InterfaceAlias;
	[Write, ValueMap{"Absent","Present"}, Values{"Absent","Present"}] String Ensure;
};</pre>
Create a module manifest for the PowerShell module that was created in the previous step:
<pre class="lang:ps decode:true">New-ModuleManifest -Path "$env:ProgramFiles\WindowsPowershell\Modules\cJumboFrames\cJumboFrames.psd1" -Author 'Mike F Robbins' -CompanyName 'mikefrobbins.com' -RootModule 'cJumboFrames' -Description 'Module with DSC Resource for Jumbo Frames' -PowerShellVersion 4.0 -FunctionsToExport '*.TargetResource' -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource9.jpg"><img class="alignnone size-full wp-image-10518" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource9.jpg" alt="dsc-resource9" width="877" height="120" /></a>

You'll end up with this module manifest (psd1 file):
<pre class="lang:ps decode:true" title="cJumboFrames.psd1">#
# Module manifest for module 'cJumboFrames'
#
# Generated by: Mike F Robbins
#
# Generated on: 10/1/2014
#

@{

# Script module or binary module file associated with this manifest.
RootModule = 'cJumboFrames'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '866d8b02-a207-4d3c-add3-70acd4df41a2'

# Author of this module
Author = 'Mike F Robbins'

# Company or vendor of this module
CompanyName = 'mikefrobbins.com'

# Copyright statement for this module
Copyright = '(c) 2014 Mike F Robbins. All rights reserved.'

# Description of the functionality provided by this module
Description = 'Module with DSC Resource for Jumbo Frames'

# Minimum version of the Windows PowerShell engine required by this module
PowerShellVersion = '4.0'

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
# FormatsToProcess = @()

# Modules to import as nested modules of the module specified in RootModule/ModuleToProcess
# NestedModules = @()

# Functions to export from this module
FunctionsToExport = '*.TargetResource'

# Cmdlets to export from this module
CmdletsToExport = '*'

# Variables to export from this module
VariablesToExport = '*'

# Aliases to export from this module
AliasesToExport = '*'

# List of all modules packaged with this module
# ModuleList = @()

# List of all files packaged with this module
# FileList = @()

# Private data to pass to the module specified in RootModule/ModuleToProcess
# PrivateData = ''

# HelpInfo URI of this module
# HelpInfoURI = ''

# Default prefix for commands exported from this module. Override the default prefix using Import-Module -Prefix.
# DefaultCommandPrefix = ''

}</pre>
Make the necessary changes to the script module (psm1 file) to get, set, and test for the value of the jumbo frames settings on the specified network card:
<pre class="lang:ps decode:true " title="cJumboFrames.psm1">#region Get Jumbo Frames Setting
function Get-TargetResource {
	[CmdletBinding()]
	[OutputType([Hashtable])]
	param (
		[Parameter(Mandatory)]
		[String]$InterfaceAlias
	)

    $CurrentSettings = Get-NetAdapterAdvancedProperty -InterfaceAlias $InterfaceAlias |
                       Where-Object RegistryKeyword -eq '*JumboPacket'

    Write-Verbose -Message 'Determining if the NIC manufacturer uses 1500 or 1514 for the default packet size setting.'
    [int]$NormalPacket = [int]$CurrentSettings.DefaultRegistryValue
    [int]$JumboPacket = [int]$CurrentSettings.DefaultRegistryValue + 7500

    $returnValue = @{
        InterfaceAlias = $InterfaceAlias
        Ensure = switch ($CurrentSettings.RegistryValue) {
            $JumboPacket {'Present'}
            $NormalPacket {'Absent'}
        }
	}

	$returnValue
}
#endregion

#region Set Jumbo Frames Setting
function Set-TargetResource {
	[CmdletBinding()]
	param (
		[Parameter(Mandatory)]
		[String]$InterfaceAlias,

        [Parameter(Mandatory)]
		[ValidateSet('Absent','Present')]
		[String]$Ensure
	)

    $CurrentSettings = Get-NetAdapterAdvancedProperty -InterfaceAlias $InterfaceAlias |
                       Where-Object RegistryKeyword -eq '*JumboPacket'

    Write-Verbose -Message 'Determining if the NIC manufacturer uses 1500 or 1514 for the default packet size setting.'
    [int]$NormalPacket = [int]$CurrentSettings.DefaultRegistryValue
    [int]$JumboPacket = [int]$CurrentSettings.DefaultRegistryValue + 7500

    switch ($Ensure) {
        'Absent' {[int]$DesiredSetting = $NormalPacket}
        'Present' {[int]$DesiredSetting = $JumboPacket}
    }

    if ($CurrentSettings.RegistryValue -ne $DesiredSetting -and $CurrentSettings.RegistryValue -ne $null) {
        Set-NetAdapterAdvancedProperty -InterfaceAlias $InterfaceAlias -RegistryKeyword '*JumboPacket' -RegistryValue $DesiredSetting -PassThru 
    }

}
#endregion

#region Test Jumbo Frames Setting
function Test-TargetResource {
	[CmdletBinding()]
	[OutputType([Boolean])]
	param (
		[Parameter(Mandatory)]
		[String]$InterfaceAlias,

        [Parameter(Mandatory)]
		[ValidateSet('Absent','Present')]
		[String]$Ensure
	)

    $CurrentSettings = Get-NetAdapterAdvancedProperty -InterfaceAlias $InterfaceAlias |
                       Where-Object RegistryKeyword -eq '*JumboPacket'

    Write-Verbose -Message 'Determining if the NIC manufacturer uses 1500 or 1514 for the default packet size setting.'
    [int]$NormalPacket = [int]$CurrentSettings.DefaultRegistryValue
    [int]$JumboPacket = [int]$CurrentSettings.DefaultRegistryValue + 7500

    switch ($Ensure) {
        'Absent' {[int]$DesiredSetting = $NormalPacket}
        'Present' {[int]$DesiredSetting = $JumboPacket}
    }

    if ($CurrentSettings.RegistryValue -ne $DesiredSetting -and $CurrentSettings.RegistryValue -ne $null) {
        Write-Verbose -Message "Jumbo Frames setting is Non-Compliant! Value should be $DesiredSetting - Detected value is: $($CurrentSettings.RegistryValue)."
        [Boolean]$result = $false
    }
    elseif ($CurrentSettings.RegistryValue -eq $DesiredSetting) {
        Write-Verbose -Message 'Jumbo Frames setting matches the desired state.'
        [Boolean]$result = $true
    }

    $result
}
#endregion</pre>
The cJumboFrames resource needs to also exist on any servers where a DSC configuration will be applied that replies on this resource. There are several tutorials on the web for distributing a resource so I won't duplicate that content here. I've simply copied the cJumboFrames folder from the Windows 8.1 workstation I've been developing it on to the same location on the SQL02 server where I plan to apply a configuration that will use it.

Create a DSC configuration to test the custom cJumboFrames DSC resource:
<pre class="lang:ps decode:true">configuration TestJumboFrames {
    
    param (
        [Parameter(Mandatory)]
        [string[]]$ComputerName
    )

    Import-DscResource -ModuleName cJumboFrames

    node $ComputerName {
        cJumboFrames iSCSINIC {
            InterfaceAlias = 'Ethernet 2'
            Ensure = 'Present'
        }
    }
}</pre>
Run the configuration, specifying SQL02 as the computer name. This creates a mof configuration file that is specific to the SQL02 test server.
<pre class="lang:ps decode:true ">TestJumboFrames -ComputerName SQL02</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource10.jpg"><img class="alignnone size-full wp-image-10521" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource10.jpg" alt="dsc-resource10" width="877" height="178" /></a>

Before applying the configuration, let's check the current jumbo frames setting on SQL02:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL02 {
    Get-NetAdapterAdvancedProperty -InterfaceAlias 'Ethernet 2' |
    Where-Object RegistryKeyword -eq '*JumboPacket'
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource11.jpg"><img class="alignnone size-full wp-image-10523" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource11.jpg" alt="dsc-resource11" width="877" height="527" /></a>

As shown in the previous results, we can see that the network card is set to a standard packet size of 1514 bytes.

Now to apply the DSC configuration that we previously created to SQL02:
<pre class="lang:ps decode:true">Start-DscConfiguration -ComputerName SQL02 -Path .\TestJumboFrames -Wait -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource12.jpg"><img class="alignnone size-full wp-image-10525" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource12.jpg" alt="dsc-resource12" width="877" height="312" /></a>

Check the jumbo frames settings again now that the configuration has been applied:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL02 {
    Get-NetAdapterAdvancedProperty -InterfaceAlias 'Ethernet 2' |
    Where-Object RegistryKeyword -eq '*JumboPacket'
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource13.jpg"><img class="alignnone size-full wp-image-10526" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource13.jpg" alt="dsc-resource13" width="877" height="528" /></a>

You can see that jumbo frames have been enabled for that network card and the maximum packet size is now set to 9014 bytes.

I also want to show you the jumbo frames settings from another Windows Server 2012 R2 machine that has a network card produced by a different manufacturer. This should help you to understand why I wrote the code in the script module the way I did:
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName SQL01 {
    Get-NetAdapterAdvancedProperty -InterfaceAlias 'NIC1' |
    Where-Object RegistryKeyword -eq '*JumboPacket'
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource14.jpg"><img class="alignnone size-full wp-image-10576" src="http://mikefrobbins.com/wp-content/uploads/2014/10/dsc-resource14.jpg" alt="dsc-resource14" width="877" height="528" /></a>

Any thoughts or feedback for improvements on this resource or the process of creating a DSC resource would be appreciated.

Want to learn more about creating DSC resources? There's a section on that topic in the DSC book which can be downloaded from <a href="http://powershell.org/wp/ebooks/" target="_blank">the free eBook section on PowerShell.org</a>.

Looking for DSC resources? While there are several that ship with PowerShell version 4, the PowerShell team has released <a href="http://gallery.technet.microsoft.com/DSC-Resource-Kit-All-c449312d" target="_blank">7 different waves of DSC resources</a>. You can also find community created DSC resources on <a href="https://github.com/PowerShellOrg/DSC/tree/master/Resources" target="_blank">PowerShell.org's GitHub</a>.

Have questions about DSC that aren't related to this blog post? I recommend posting them in <a href="http://powershell.org/wp/forums/" target="_blank">the DSC forum on PowerShell.org</a>.

µ