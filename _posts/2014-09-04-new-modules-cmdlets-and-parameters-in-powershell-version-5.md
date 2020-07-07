---
ID: 10384
post_title: 'New Modules, Cmdlets, &#038; Parameters in PowerShell Version 5'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/09/04/new-modules-cmdlets-and-parameters-in-powershell-version-5/
published: true
post_date: 2014-09-04 07:30:03
---
Think keeping up with the changes between major versions of PowerShell is difficult? Well just try keeping up with the changes that are added and removed in preview versions.

The following is a list of the new modules, cmdlets, and parameters that have been added so far in PowerShell version 5 grouped by the different preview releases.

<hr />

<strong>Windows Technical Preview Enterprise Edition (Windows 10)</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-build9879.jpg"><img class="alignnone size-full wp-image-10818" src="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-build9879.jpg" alt="win10pv-build9879" width="859" height="132" /></a>

<strong>PowerShell Version 5 Build 9879</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/psversiontable-build9879.jpg"><img class="alignnone size-full wp-image-10819" src="http://mikefrobbins.com/wp-content/uploads/2014/09/psversiontable-build9879.jpg" alt="psversiontable-build9879" width="859" height="204" /></a>

<span style="text-decoration: underline;">New cmdlets and the module they were added to:</span>

PnpDevice
<ul>
	<li>Disable-PnpDevice</li>
	<li>Enable-PnpDevice</li>
	<li>Get-PnpDevice</li>
	<li>Get-PnpDeviceProperty</li>
</ul>
PSDesiredStateConfiguration
<ul>
	<li>Find-DscResource</li>
</ul>
Microsoft.PowerShell.Core
<ul>
	<li>Get-SerializedCommand</li>
</ul>
Storage
<ul>
	<li>Get-StorageEnclosureSNV</li>
	<li>Get-StorageEnclosureStorageNodeView</li>
</ul>
PcsvDevice
<ul>
	<li>Set-PcsvDeviceUserPassword</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Connect-DscConfiguration
<ul>
	<li>InstanceId</li>
</ul>
Enter-PSHostProcess
<ul>
	<li>AppDomainName</li>
</ul>
Find-Module
<ul>
	<li>AllVersions</li>
	<li>Command</li>
	<li>DscResource</li>
	<li>Filter</li>
	<li>Includes</li>
	<li>Tag</li>
</ul>
Find-Package
<ul>
	<li>Command</li>
	<li>DscResource</li>
	<li>Filter</li>
	<li>Includes</li>
</ul>
Install-Module
<ul>
	<li>InputObject</li>
</ul>
Install-Package
<ul>
	<li>AllVersions</li>
	<li>Command</li>
	<li>DscResource</li>
	<li>Filter</li>
	<li>Includes</li>
</ul>
New-AppLockerPolicy
<ul>
	<li>ServiceEnforcement</li>
</ul>
Save-Package
<ul>
	<li>AllVersions</li>
	<li>Command</li>
	<li>DscResource</li>
	<li>Filter</li>
	<li>Includes</li>
</ul>
<span style="text-decoration: underline;">Parameters removed from existing cmdlets:</span>

Connect-DscConfiguration
<ul>
	<li>JobId</li>
</ul>
Install-Module
<ul>
	<li>PSGetItemInfo</li>
</ul>
Start-DscConfiguration
<ul>
	<li>AsDisconnected</li>
</ul>

<hr />

<strong>Windows Technical Preview Enterprise Edition (Windows 10)</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-build9860.jpg"><img class="alignnone size-full wp-image-10814" src="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-build9860.jpg" alt="win10pv-build9860" width="859" height="131" /></a>

<strong>PowerShell Version 5 Build 9860</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/psversiontable-build9860.jpg"><img class="alignnone size-full wp-image-10815" src="http://mikefrobbins.com/wp-content/uploads/2014/09/psversiontable-build9860.jpg" alt="psversiontable-build9860" width="859" height="204" /></a>

<span style="text-decoration: underline;">New cmdlets and the existing module they were added to:</span>

PSDesiredStateConfiguration
<ul>
	<li>Connect-DscConfiguration</li>
</ul>
PcsvDevice
<ul>
	<li>Set-PcsvDeviceNetworkConfiguration</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Compress-Archive
<ul>
	<li>Force</li>
</ul>
Export-ODataEndpointProxy
<ul>
	<li>AllowClobber</li>
	<li>AllowUnsecureConnection</li>
	<li>CmdletAdapter</li>
	<li>Confirm</li>
	<li>CreateRequestMethod</li>
	<li>CustomData</li>
	<li>Force</li>
	<li>OutputModule</li>
	<li>UpdateRequestMethod</li>
	<li>WhatIf</li>
</ul>
Find-Package
<ul>
	<li>AllowPrereleaseVersions</li>
	<li>ConfigFile</li>
	<li>Contains</li>
	<li>SkipValidate</li>
	<li>Tag</li>
</ul>
Get-Package
<ul>
	<li>ContinueOnFailure</li>
	<li>Destination</li>
	<li>ExcludeVersion</li>
	<li>PackageSaveMode</li>
	<li>SkipDependencies</li>
</ul>
Get-PackageSource
<ul>
	<li>ConfigFile</li>
	<li>SkipValidate</li>
</ul>
Get-Volume
<ul>
	<li>StorageFileServer</li>
	<li>UniqueId</li>
</ul>
Install-Package
<ul>
	<li>AllowPrereleaseVersions</li>
	<li>ConfigFile</li>
	<li>Contains</li>
	<li>ContinueOnFailure</li>
	<li>Destination</li>
	<li>ExcludeVersion</li>
	<li>PackageSaveMode</li>
	<li>SkipDependencies</li>
	<li>SkipValidate</li>
	<li>Tag</li>
</ul>
Save-Package
<ul>
	<li>AllowPrereleaseVersions</li>
	<li>ConfigFile</li>
	<li>Contains</li>
	<li>SkipValidate</li>
	<li>Tag</li>
</ul>
Set-MpPreference
<ul>
	<li>SubmitSamplesConsent</li>
</ul>
Set-PackageSource
<ul>
	<li>ConfigFile</li>
	<li>SkipValidate</li>
</ul>
Start-DscConfiguration
<ul>
	<li>AsDisconnected</li>
</ul>
Uninstall-Package
<ul>
	<li>ContinueOnFailure</li>
	<li>Destination</li>
	<li>ExcludeVersion</li>
	<li>PackageSaveMode</li>
	<li>SkipDependencies</li>
</ul>
Unregister-PackageSource
<ul>
	<li>ConfigFile</li>
	<li>SkipValidate</li>
</ul>
<span style="text-decoration: underline;">Parameters removed from existing cmdlets:</span>

Export-ODataEndpointProxy
<ul>
	<li>OutputPath</li>
</ul>

<hr />

<strong>Windows Technical Preview Enterprise Edition (Windows 10)</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-version.jpg"><img class="alignnone size-full wp-image-10598" src="http://mikefrobbins.com/wp-content/uploads/2014/09/win10pv-version.jpg" alt="win10pv-version" width="979" height="147" /></a>

<strong>PowerShell Version 5 Build 9841</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/build9841.jpg"><img class="alignnone size-full wp-image-10588" src="http://mikefrobbins.com/wp-content/uploads/2014/09/build9841.jpg" alt="build9841" width="979" height="230" /></a>

<span style="text-decoration: underline;">New cmdlets and the existing module they were added to:</span>

Appx
<ul>
	<li>Add-AppxVolume</li>
	<li>Get-AppxVolume</li>
	<li>Mount-AppxVolume</li>
	<li>Move-AppxPackage</li>
	<li>Remove-AppxVolume</li>
	<li>Dismount-AppxVolume</li>
	<li>Get-AppxDefaultVolume</li>
	<li>Set-AppxDefaultVolume</li>
</ul>
Storage
<ul>
	<li>Get-FileShare</li>
	<li>New-FileShare</li>
	<li>Block-FileShareAccess</li>
	<li>Grant-FileShareAccess</li>
	<li>Get-FileShareAccessControlEntry</li>
	<li>Disable-PhysicalDiskIdentification</li>
	<li>Enable-PhysicalDiskIdentification</li>
	<li>Disable-StorageDiagnosticLog</li>
	<li>Enable-StorageDiagnosticLog</li>
	<li>Get-DiskSNV (alias)</li>
	<li>Get-DiskStorageNodeView</li>
	<li>Get-PhysicalDiskSNV (alias)</li>
	<li>Get-PhysicalDiskStorageNodeView</li>
	<li>Remove-FileShare</li>
	<li>Set-FileShare</li>
	<li>Revoke-FileShareAccess</li>
	<li>Unblock-FileShareAccess</li>
	<li>Get-StorageFileServer</li>
	<li>New-StorageFileServer</li>
	<li>Remove-StorageFileServer</li>
	<li>Set-StorageFileServer</li>
	<li>Get-StorageOperationalLog</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Add-AppxPackage
<ul>
	<li>Volume</li>
</ul>
Get-AppxPackage
<ul>
	<li>Volume</li>
</ul>
Export-StartLayout
<ul>
	<li>For</li>
</ul>
Import-StartLayout
<ul>
	<li>For</li>
</ul>
Get-Disk
<ul>
	<li>SerialNumber</li>
	<li>StorageNode</li>
	<li>StorageSubSystem</li>
</ul>
Get-NetIPConfiguration
<ul>
	<li>AllCompartments</li>
	<li>CompartmentId</li>
</ul>
Get-NetIPv4Protocol
<ul>
	<li>MinimumMtu</li>
</ul>
Set-NetIPv4Protocol
<ul>
	<li>MinimumMtu</li>
</ul>
Get-NetTCPSetting
<ul>
	<li>AutoReusePortRangeNumberOfPorts</li>
	<li>AutoReusePortRangeStartPort</li>
</ul>
Set-NetTCPSetting
<ul>
	<li>AutoReusePortRangeNumberOfPorts</li>
	<li>AutoReusePortRangeStartPort</li>
</ul>
Get-Partition
<ul>
	<li>StorageSubSystem</li>
</ul>
Set-Partition
<ul>
	<li>GptType</li>
	<li>MbrType</li>
</ul>
Get-PhysicalDisk
<ul>
	<li>ObjectId</li>
	<li>SerialNumber</li>
</ul>
Get-StorageEnclosure
<ul>
	<li>SerialNumber</li>
</ul>
Get-StorageNode
<ul>
	<li>Disk</li>
	<li>ObjectId</li>
	<li>Volume</li>
</ul>
Get-StoragePool
<ul>
	<li>Volume</li>
</ul>
Get-StorageSubSystem
<ul>
	<li>Disk</li>
	<li>Partition</li>
	<li>Volume</li>
</ul>
Get-Volume
<ul>
	<li>StorageNode</li>
	<li>StoragePool</li>
	<li>StorageSubSystem</li>
</ul>
Optimize-Volume
<ul>
	<li>NormalPriority</li>
</ul>
Set-PcsvDeviceBootConfiguration
<ul>
	<li>PersistentBootSource</li>
</ul>
Stop-PcsvDevice
<ul>
	<li>ShutdownType</li>
</ul>

<hr />

<strong>Windows 8.1 Enterprise Edition</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/win81-version.jpg"><img class="alignnone size-full wp-image-10601" src="http://mikefrobbins.com/wp-content/uploads/2014/09/win81-version.jpg" alt="win81-version" width="876" height="132" /></a>

<strong>Windows Management Framework 5.0 Preview September 2014:</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/build9814.jpg"><img class="alignnone size-full wp-image-10590" src="http://mikefrobbins.com/wp-content/uploads/2014/09/build9814.jpg" alt="build9814" width="877" height="205" /></a>

<span style="text-decoration: underline;">New cmdlets and the existing module they were added to:</span>

Microsoft.PowerShell.Core
<ul>
	<li>Enter-PSHostProcess</li>
	<li>Exit-PSHostProcess</li>
</ul>
Microsoft.PowerShell.Utility
<ul>
	<li>ConvertFrom-String</li>
	<li>Wait-Debugger</li>
</ul>
OneGet
<ul>
	<li>Get-PackageProvider</li>
	<li>Register-PackageSource</li>
	<li>Unregister-PackageSource</li>
	<li>Set-PackageSource</li>
	<li>Save-Package</li>
</ul>
PowerShellGet
<ul>
	<li>Get-PSRepository</li>
	<li>Set-PSRepository</li>
	<li>Register-PSRepository</li>
	<li>Unregister-PSRepository</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Export-ODataEndpointProxy
<ul>
	<li>ResourceNameMapping</li>
</ul>
Find-Module
<ul>
	<li>Repository</li>
</ul>
Find-Package
<ul>
	<li>AllVersions</li>
	<li>Credential</li>
	<li>Force</li>
	<li>ForceBootstrap</li>
	<li>IncludeDependencies</li>
	<li>MessageResolver</li>
	<li>OneGetProvider</li>
	<li>ProviderName</li>
	<li>PublishLocation</li>
	<li>Scope</li>
</ul>
Get-CmsMessage
<ul>
	<li>LiteralPath</li>
	<li>Path</li>
</ul>
Get-Package
<ul>
	<li>Force</li>
	<li>ForceBootstrap</li>
	<li>InstallationPolicy</li>
	<li>InstallUpdate</li>
	<li>Location</li>
	<li>MaximumVersion</li>
	<li>MessageResolver</li>
	<li>MinimumVersion</li>
	<li>OneGetProvider</li>
	<li>ProviderName</li>
	<li>RequiredVersion</li>
</ul>
Get-PackageSource
<ul>
	<li>Force</li>
	<li>ForceBootstrap</li>
	<li>MessageResolver</li>
	<li>OneGetProvider</li>
	<li>ProviderName</li>
	<li>PublishLocation</li>
	<li>Scope</li>
</ul>
Install-Module
<ul>
	<li>Repository</li>
</ul>
Install-Package
<ul>
	<li>Credential</li>
	<li>ForceBootstrap</li>
	<li>InputObject</li>
	<li>InstallationPolicy</li>
	<li>InstallUpdate</li>
	<li>Location</li>
	<li>MessageResolver</li>
	<li>OneGetProvider</li>
	<li>ProviderName</li>
	<li>PublishLocation</li>
	<li>Scope</li>
</ul>
Protect-CmsMessage
<ul>
	<li>LiteralPath</li>
	<li>OutFile</li>
	<li>Path</li>
</ul>
Publish-Module
<ul>
	<li>Repository</li>
</ul>
Save-Help
<ul>
	<li>FullyQualifiedModule</li>
</ul>
Start-Transcript
<ul>
	<li>IncludeInvocationHeader</li>
	<li>OutputDirectory</li>
</ul>
Uninstall-Package
<ul>
	<li>ForceBootstrap</li>
	<li>InstallationPolicy</li>
	<li>InstallUpdate</li>
	<li>Location</li>
	<li>MaximumVersion</li>
	<li>MessageResolver</li>
	<li>MinimumVersion</li>
	<li>OneGetProvider</li>
	<li>ProviderName</li>
	<li>RequiredVersion</li>
</ul>
Unprotect-CmsMessage
<ul>
	<li>EventLogRecord</li>
	<li>IncludeContext</li>
	<li>LiteralPath</li>
	<li>Path</li>
	<li>To</li>
</ul>
Update-Help
<ul>
	<li>FullyQualifiedModule</li>
</ul>
<span style="text-decoration: underline;">Removed cmdlets:</span>
<ul>
	<li>Add-PackageSource</li>
	<li>Remove-PackageSource</li>
	<li>Get-ModuleSource</li>
	<li>Register-ModuleSource</li>
	<li>Unregister-ModuleSource</li>
</ul>
<span style="text-decoration: underline;">Parameters removed from existing cmdlets:</span>

Find-Module
<ul>
	<li>Source</li>
</ul>
Find-Package
<ul>
	<li>Metadata</li>
	<li>Provider</li>
</ul>
Get-Package
<ul>
	<li>Metadata</li>
	<li>Provider</li>
</ul>
Get-PackageSource
<ul>
	<li>Provider</li>
</ul>
Install-Module
<ul>
	<li>Source</li>
</ul>
Install-Package
<ul>
	<li>InstallationOptions</li>
	<li>Metadata</li>
	<li>Package</li>
	<li>Provider</li>
</ul>
Publish-Module
<ul>
	<li>Destination</li>
</ul>
Uninstall-Package
<ul>
	<li>InstallOptions</li>
	<li>Metadata</li>
	<li>Provider</li>
</ul>

<hr />

<strong>Windows Management Framework 5.0 Experimental Release July 2014:</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/build9789.jpg"><img class="alignnone size-full wp-image-10595" src="http://mikefrobbins.com/wp-content/uploads/2014/09/build9789.jpg" alt="build9789" width="877" height="202" /></a>

<span style="text-decoration: underline;">New modules and their cmdlets:</span>

Microsoft.PowerShell.Archive
<ul>
	<li>Compress-Archive</li>
	<li>Expand-Archive</li>
</ul>
Microsoft.PowerShell.ODataUtils
<ul>
	<li>Export-ODataEndpointProxy</li>
</ul>
<span style="text-decoration: underline;">Other new cmdlets and the existing module they were added to:</span>

PSDesiredStateConfiguration
<ul>
	<li>Get-DscConfigurationStatus</li>
	<li>Compare-DscConfiguration</li>
	<li>Publish-DscConfiguration</li>
	<li>Update-DscConfiguration</li>
</ul>
Microsoft.PowerShell.Utility
<ul>
	<li>Debug-Runspace</li>
	<li>Get-Runspace</li>
	<li>Disable-RunspaceDebug</li>
	<li>Enable-RunspaceDebug</li>
	<li>Get-RunspaceDebug</li>
</ul>
Microsoft.PowerShell.Security
<ul>
	<li>Get-CmsMessage</li>
	<li>Protect-CmsMessage</li>
	<li>Unprotect-CmsMessage</li>
</ul>
PowerShellGet
<ul>
	<li>Get-ModuleSource</li>
	<li>Register-ModuleSource</li>
	<li>Unregister-ModuleSource</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Find-Module
<ul>
	<li>Source</li>
</ul>
Install-Module
<ul>
	<li>Source</li>
</ul>
Publish-Module
<ul>
	<li>Destination</li>
	<li>Tags</li>
</ul>
Import-Module
<ul>
	<li>FullyQualifiedName</li>
</ul>
Remove-Module
<ul>
	<li>FullyQualifiedName</li>
</ul>
Get-Command
<ul>
	<li>FullyQualifiedModule</li>
</ul>
Import-PSSession
<ul>
	<li>FullyQualifiedModule</li>
</ul>
Export-PSSession
<ul>
	<li>FullyQualifiedModule</li>
</ul>
New-ModuleManifest
<ul>
	<li>IconUri</li>
	<li>LicenseUri</li>
	<li>ProjectUri</li>
	<li>ReleaseNotes</li>
	<li>Tags</li>
</ul>
New-PSSessionOption
<ul>
	<li>MaxConnectionRetryCount</li>
</ul>
Out-Default
<ul>
	<li>Transcript</li>
</ul>
Restart-Computer
<ul>
	<li>Soft</li>
</ul>
<span style="text-decoration: underline;">Removed cmdlets:</span>
<ul>
	<li>Get-StreamHash</li>
</ul>
<span style="text-decoration: underline;">Parameters removed from existing cmdlets:</span>

Find-Package
<ul>
	<li>AllowPrereleaseVersions</li>
	<li>AllVersions</li>
	<li>Hint</li>
	<li>LeavePartialPackageInstalled</li>
	<li>LocalOnly</li>
</ul>
Install-Package
<ul>
	<li>AllowPrereleaseVersions</li>
	<li>AllVersions</li>
	<li>ForceX86</li>
	<li>Hint</li>
	<li>IgnoreDependencies</li>
	<li>InstallArguments</li>
	<li>LeavePartialPackageInstalled</li>
	<li>LocalOnly</li>
	<li>OverrideArguments</li>
	<li>PackageParameters</li>
</ul>
Publish-Module
<ul>
	<li>Tag</li>
</ul>

<hr />

<strong>Windows Management Framework 5.0 Preview May 2014:</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/build9740.jpg"><img class="alignnone size-full wp-image-10593" src="http://mikefrobbins.com/wp-content/uploads/2014/09/build9740.jpg" alt="build9740" width="877" height="203" /></a>

<span style="text-decoration: underline;">New modules and their cmdlets:</span>

PowerShellGet
<ul>
	<li>Find-Module</li>
	<li>Install-Module</li>
	<li>Publish-Module</li>
	<li>Update-Module</li>
</ul>

<hr />

<strong>Windows Management Framework 5.0 Preview April 2014:</strong>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/09/build9701.jpg"><img class="alignnone size-full wp-image-10592" src="http://mikefrobbins.com/wp-content/uploads/2014/09/build9701.jpg" alt="build9701" width="877" height="203" /></a>

<span style="text-decoration: underline;">New modules and their cmdlets:</span>

NetworkSwitch
<ul>
	<li>Disable-NetworkSwitchEthernetPort</li>
	<li>Disable-NetworkSwitchFeature</li>
	<li>Disable-NetworkSwitchVlan</li>
	<li>Enable-NetworkSwitchEthernetPort</li>
	<li>Enable-NetworkSwitchFeature</li>
	<li>Enable-NetworkSwitchVlan</li>
	<li>Get-NetworkSwitchEthernetPort</li>
	<li>Get-NetworkSwitchFeature</li>
	<li>Get-NetworkSwitchGlobalData</li>
	<li>Get-NetworkSwitchVlan</li>
	<li>New-NetworkSwitchVlan</li>
	<li>Remove-NetworkSwitchEthernetPortIPAddress</li>
	<li>Remove-NetworkSwitchVlan</li>
	<li>Restore-NetworkSwitchConfiguration</li>
	<li>Save-NetworkSwitchConfiguration</li>
	<li>Set-NetworkSwitchEthernetPortIPAddress</li>
	<li>Set-NetworkSwitchPortMode</li>
	<li>Set-NetworkSwitchPortProperty</li>
	<li>Set-NetworkSwitchVlanProperty</li>
</ul>
OneGet
<ul>
	<li>Add-PackageSource</li>
	<li>Find-Package</li>
	<li>Get-Package</li>
	<li>Get-PackageSource</li>
	<li>Install-Package</li>
	<li>Remove-PackageSource</li>
	<li>Uninstall-Package</li>
</ul>
<span style="text-decoration: underline;">Other new cmdlets and the existing module they were added to:</span>

Microsoft.PowerShell.Core
<ul>
	<li>Debug-Job</li>
</ul>
Microsoft.PowerShell.Management
<ul>
	<li>Get-ItemPropertyValue</li>
</ul>
Microsoft.PowerShell.Utility
<ul>
	<li>Get-StreamHash</li>
</ul>
<span style="text-decoration: underline;">Parameters added to existing cmdlets:</span>

Get-FileHash
<ul>
	<li>InputStream</li>
</ul>
New-DSCCheckSum
<ul>
	<li>Confirm</li>
	<li>WhatIf</li>
</ul>
Test-DscConfiguration
<ul>
	<li>Detailed</li>
</ul>
Register-ScheduledJob
<ul>
	<li>RunEvery</li>
</ul>
Set-ScheduledJob
<ul>
	<li>RunEvery</li>
</ul>
Select-Object
<ul>
	<li>SkipLast</li>
</ul>
Stop-Service
<ul>
	<li>NoWait</li>
</ul>
Update-Help
<ul>
	<li>Confirm</li>
	<li>WhatIf</li>
</ul>

<hr />

I'll be demonstrating many of these new features in my "<a href="http://www.sqlsaturday.com/viewsession.aspx?sat=342&amp;sessionid=23911" target="_blank">What's New in PowerShell Version 5</a>" session at  <a href="http://www.sqlsaturday.com/342/eventhome.aspx" target="_blank">SQL Saturday #342</a> in Mobile, AL on September 27th.

µ