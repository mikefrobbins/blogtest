---
ID: 16281
post_title: 'The PowerShell Iron Scripter: My solution to prequel puzzle 2'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/01/26/the-powershell-iron-scripter-my-solution-to-prequel-puzzle-2/
published: true
post_date: 2018-01-26 07:30:25
---
As I mentioned in my previous blog article, each week leading up to the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit 2018</a>, <a href="https://powershell.org/" target="_blank" rel="noopener">PowerShell.org</a> will be posting an <a href="https://powershell.org/2018/01/04/iron-scripter-prequel/" target="_blank" rel="noopener">iron scripter prequel puzzle</a> on their website. As their website states, think of the iron scripter as the successor to the scripting games.

If you haven't done so already, I recommend reading <a href="http://mikefrobbins.com/2018/01/20/the-powershell-iron-scripter-my-solution-to-prequel-puzzle-1/" target="_blank" rel="noopener">my solution to the Iron Scripter prequel puzzle 1</a> because some things are glossed over in this blog article that were covered in detail in that previous one.

<a href="https://powershell.org/2018/01/21/iron-scripter-2018-prequel-puzzle-2/" target="_blank" rel="noopener">Prequel puzzle 2</a> provides you with an older script that looks like it was written back in the VBScript days. It retrieves information about the operating system, memory, and logical disks. This entry is fairly similar to my previous one as it queries all of the remote computers at once using a CIM session which is created with the <em>New-CimSession</em> cmdlet that was introduced in PowerShell version 3.0. Using the CIM cmdlets instead of the older WMI ones allows it to run in PowerShell Core 6.0.

The built-in DriveType enumeration is used for parameter validation and tabbed expansion / intellisense of the DriveType parameter.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter1a.jpg"><img class="alignnone size-full wp-image-16285" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter1a.jpg" alt="" width="859" height="177" /></a>

I also decided to add the operating system ReleaseId which has to be retrieved from the registry.
<pre class="lang:ps decode:true" title="Get-MrSystemInfo">#Requires -Version 3.0
function Get-MrSystemInfo {

&lt;#
.SYNOPSIS
    Retrieves information about the operating system, memory, and logical disks from the specified system.
 
.DESCRIPTION
    Get-MrSystemInfo is an advanced function that retrieves information about the operating system, memory,
    and logical disks from the specified system.
 
.PARAMETER CimSession
    Specifies the CIM session to use for this function. Enter a variable that contains the CIM session or a command that
    creates or gets the CIM session, such as the New-CimSession or Get-CimSession cmdlets. For more information, see
    about_CimSessions.

.PARAMETER DriveType
    Specifies the type of drive to query the information for. By default, all drive types are returned, but they can be
    narrowed down to a specific type of drive such as only fixed disks. The parameter autocompletes based on the built-in
    DriveType enumeration. 
 
.EXAMPLE
     Get-MrSystemInfo

.EXAMPLE
     Get-MrSystemInfo -DriveType Fixed

.EXAMPLE
     Get-MrSystemInfo -CimSession (New-CimSession -ComputerName Server01, Server02)

.EXAMPLE
     Get-MrSystemInfo -DriveType Fixed -CimSession (New-CimSession -ComputerName Server01, Server02)
 
.INPUTS
    None
 
.OUTPUTS
    Mr.SystemInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    [OutputType('Mr.SystemInfo')]
    param (
        [Microsoft.Management.Infrastructure.CimSession[]]$CimSession,

        [System.IO.DriveType]$DriveType
    )

    $Params = @{
        ErrorAction = 'SilentlyContinue'
        ErrorVariable = 'Problem'
    }

    if ($PSBoundParameters.CimSession) {
        $Params.CimSession = $CimSession
    }

    $OSInfo = Get-CimInstance @Params -ClassName Win32_OperatingSystem -Property CSName, Caption, Version, ServicePackMajorVersion, ServicePackMinorVersion,
                                                 Manufacturer, WindowsDirectory, Locale, FreePhysicalMemory, TotalVirtualMemorySize, FreeVirtualMemory
    
    $ReleaseId = Invoke-CimMethod @Params -Namespace root\cimv2 -ClassName StdRegProv -MethodName GetSTRINGvalue -Arguments @{
                 hDefKey=[uint32]2147483650; sSubKeyName='SOFTWARE\Microsoft\Windows NT\CurrentVersion'; sValueName='ReleaseId'}

    if ($PSBoundParameters.DriveType) {
        $Params.Filter = "DriveType = $($DriveType.value__)" 
    }

    $LogicalDisk = Get-CimInstance @Params -ClassName Win32_LogicalDisk -Property SystemName, DeviceID, Description, Size, FreeSpace, Compressed

    foreach ($OS in $OSInfo) {
    
        foreach ($Disk in $LogicalDisk | Where-Object SystemName -eq $OS.CSName) {
            if (-not $PSBoundParameters.CimSession) {
                $ReleaseId.PSComputerName = $OS.CSName
            }

            [pscustomobject]@{
                ComputerName = $OS.CSName
                OSName = $OS.Caption
                OSVersion = $OS.Version
                ReleaseId = ($ReleaseId | Where-Object PSComputerName -eq $OS.CSName).sValue
                ServicePackMajorVersion = $OS.ServicePackMajorVersion
                ServicePackMinorVersion = $OS.ServicePackMinorVersion
                OSManufacturer = $OS.Manufacturer
                WindowsDirectory = $OS.WindowsDirectory
                Locale = [int]"0x$($OS.Locale)"
                AvailablePhysicalMemory = $OS.FreePhysicalMemory
                TotalVirtualMemory = $OS.TotalVirtualMemorySize
                AvailableVirtualMemory = $OS.FreeVirtualMemory
                Drive = $Disk.DeviceID
                DriveType = $Disk.Description
                Size = $Disk.Size
                FreeSpace = $Disk.FreeSpace
                Compressed = $Disk.Compressed
                PSTypeName = 'Mr.SystemInfo'
            }
    
        }
    
    }

    foreach ($p in $Problem) {
        Write-Warning -Message "An error occurred on $($p.OriginInfo). $($p.Exception.Message)"
    }

}</pre>
The function shown in the previous example outputs the raw data returned by the cmdlets as a single type of object with a custom name. None of the disk or memory sizes are converted in case the person working with this function wants to use that raw information. The only exception is that locale is converted to decimal instead of returning it as the default hexadecimal value.

Who really wants to see drives or memory returned in bytes or kilobytes when they run a PowerShell command? A types.ps1xml file is used to extend the types of the returned object so the default output is much more user friendly.
<pre class="lang:ps decode:true" title="MrIronScripter.types.ps1xml">&lt;Types&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;MemberSet&gt;
          &lt;Name&gt;PSStandardMembers&lt;/Name&gt;
          &lt;Members&gt;
            &lt;PropertySet&gt;
              &lt;Name&gt;DefaultDisplayPropertySet&lt;/Name&gt;
              &lt;ReferencedProperties&gt;
                &lt;Name&gt;ComputerName&lt;/Name&gt;
                &lt;Name&gt;OSName&lt;/Name&gt;
                &lt;Name&gt;OSVersion&lt;/Name&gt;
                &lt;Name&gt;ReleaseId&lt;/Name&gt;
                &lt;Name&gt;ServicePack&lt;/Name&gt;
                &lt;Name&gt;OSManufacturer&lt;/Name&gt;
                &lt;Name&gt;WindowsDirectory&lt;/Name&gt;
                &lt;Name&gt;LocaleName&lt;/Name&gt;
                &lt;Name&gt;AvailableRAM(GB)&lt;/Name&gt;
                &lt;Name&gt;TotalVM(GB)&lt;/Name&gt;
                &lt;Name&gt;AvailableVM(GB)&lt;/Name&gt;
                &lt;Name&gt;Drive&lt;/Name&gt;
                &lt;Name&gt;DriveType&lt;/Name&gt;
                &lt;Name&gt;Size(GB)&lt;/Name&gt;
                &lt;Name&gt;FreeSpace(GB)&lt;/Name&gt;
                &lt;Name&gt;PercentUsed&lt;/Name&gt;
                &lt;Name&gt;Compressed&lt;/Name&gt;
              &lt;/ReferencedProperties&gt;
            &lt;/PropertySet&gt;
          &lt;/Members&gt;
        &lt;/MemberSet&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
      &lt;ScriptProperty&gt;
          &lt;Name&gt;ServicePack&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "$($this.ServicePackMajorVersion).$($this.ServicePackMinorVersion)"
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;LocaleName&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            ([System.Globalization.CultureInfo]($this.Locale)).Name
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;AvailableRAM(GB)&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f ($this.AvailablePhysicalMemory / 1MB)
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;TotalVM(GB)&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f ($this.TotalVirtualMemory / 1MB)
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;AvailableVM(GB)&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f ($this.AvailableVirtualMemory / 1MB)
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;Size(GB)&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f ($this.Size / 1GB)
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;FreeSpace(GB)&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f ($this.FreeSpace / 1GB)
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
    &lt;Type&gt;
      &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
      &lt;Members&gt;
        &lt;ScriptProperty&gt;
          &lt;Name&gt;PercentUsed&lt;/Name&gt;
          &lt;GetScriptBlock&gt;
            "{0:N2}" -f (100 - ($this.FreeSpace / $this.Size * 100))
          &lt;/GetScriptBlock&gt;
        &lt;/ScriptProperty&gt;
      &lt;/Members&gt;
    &lt;/Type&gt;
&lt;/Types&gt;</pre>
If you're interested in learning more about types in PowerShell, I recommend taking a look at the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_types.ps1xml?view=powershell-5.1" target="_blank" rel="noopener">About_Types.ps1xml</a> help topic.

Even through I've specified the default properties to return in the types.ps1xml file that was previously listed, I overwrite those defaults in a format.ps1xml file for its table view. I could have also provided a list view in the format.ps1xml file which would have eliminated the need to list the default ones in the types.ps1xml file, but I wanted to show how one of these files could be used to overwrite the other in one scenario, but not another. I've only shown the pertinent portion of the format.ps1xml file in the following example. See <a href="https://github.com/mikefrobbins/IronScripter" target="_blank" rel="noopener">my IronScripter repository on GitHub</a> for the entire file and module.
<pre class="lang:ps decode:true" title="MrIronScripter.format.ps1xml">&lt;View&gt;
    &lt;Name&gt;Mr.SystemInfo&lt;/Name&gt;
    &lt;ViewSelectedBy&gt;
        &lt;TypeName&gt;Mr.SystemInfo&lt;/TypeName&gt;
    &lt;/ViewSelectedBy&gt;
    &lt;TableControl&gt;
        &lt;TableHeaders&gt;
                &lt;TableColumnHeader&gt;
                &lt;Width&gt;16&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;                   
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;50&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;18&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;6&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;18&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
        &lt;/TableHeaders&gt;
        &lt;TableRowEntries&gt;
            &lt;TableRowEntry&gt;
                &lt;TableColumnItems&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;ComputerName&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;OSName&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;AvailableRAM(GB)&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;Drive&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;FreeSpace(GB)&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                &lt;/TableColumnItems&gt;
            &lt;/TableRowEntry&gt;
            &lt;/TableRowEntries&gt;
    &lt;/TableControl&gt;
&lt;/View&gt;</pre>
As with learning more about types, if you're interested in learning more about modifying the default display of objects in PowerShell, I recommend taking a look at the <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_format.ps1xml?view=powershell-5.1" target="_blank" rel="noopener">About_Format.ps1xml</a> help topic.

The types.ps1xml file has to be specified in the TypesToProcess section of the module manifest and the format.ps1xml file must be specified in the FormatsToProcess section. Once again, I'm only showing the relevant portion of the module manifest.
<pre class="lang:ps decode:true " title="MrIronScripter.psd1"># Type files (.ps1xml) to be loaded when importing this module
TypesToProcess = 'MrIronScripter.types.ps1xml'

# Format files (.ps1xml) to be loaded when importing this module
FormatsToProcess = 'MrIronScripter.format.ps1xml'

# Modules to import as nested modules of the module specified in RootModule/ModuleToProcess
# NestedModules = @()

# Functions to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no functions to export.
FunctionsToExport = 'Get-MrMonitorInfo', 'Get-MrSystemInfo'

# Cmdlets to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no cmdlets to export.
CmdletsToExport = ''

# Variables to export from this module
# VariablesToExport = @()

# Aliases to export from this module, for best performance, do not use wildcards and do not delete the entry, use an empty array if there are no aliases to export.
AliasesToExport = ''</pre>
Running the function returns a nice looking table view with five properties. Notice the memory and freespace are returned in gigabytes even though the function itself didn't convert those values to gigabytes. That's because those properties are defined as script properties in the types.ps1xml file.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter2a.jpg"><img class="alignnone size-full wp-image-16287" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter2a.jpg" alt="" width="859" height="172" /></a>

Piping to <em>Format-List</em> shows additional properties and their values, but not all of the properties.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter3a.jpg"><img class="alignnone size-full wp-image-16288" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter3a.jpg" alt="" width="859" height="877" /></a>

Piping to <em>Format-List</em> and specifying all properties returns all of them. Certain commands will still hide some of their properties even in this scenario and you'll either need to add the <em>-Force</em> parameter or pipe to <em>Select-Object -Property *</em> instead to view all of them along with their values.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter4a.jpg"><img class="alignnone size-full wp-image-16289" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter4a.jpg" alt="" width="859" height="428" /></a>

Piping to <em>Get-Member</em> sheds some light on the different types of properties that are returned by this function.

<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter5a.jpg"><img class="alignnone size-full wp-image-16290" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter5a.jpg" alt="" width="859" height="579" /></a>

The <em>Get-MrSystemInfo</em> function and the associated files shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/IronScripter" target="_blank" rel="noopener">my IronScripter repository on GitHub</a>. They can also be installed from the PowerShell Gallery using <em>Install-Module</em> which is part of the PowerShellGet module that ships with PowerShell version 5.0 and higher. PowerShellGet can be downloaded and installed on PowerShell version 3.0 and higher, although the module shown in this blog article requires at least PowerShell version 4.0.
<pre class="lang:ps decode:true">Install-Module -Name MrIronScripter -Force
Get-Module -Name MrIronScripter -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter6a.jpg"><img class="alignnone size-full wp-image-16295" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter6a.jpg" alt="" width="859" height="214" /></a>

If you have a previous version installed and want to install the most recent version, <em>Update-Module</em> could be used instead.
<pre class="lang:ps decode:true">Get-Module -Name MrIronScripter -ListAvailable
Update-Module -Name MrIronScripter -Force
Get-Module -Name MrIronScripter -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter7a.jpg"><img class="alignnone size-full wp-image-16296" src="http://mikefrobbins.com/wp-content/uploads/2018/01/prequel2-ironscripter7a.jpg" alt="" width="859" height="382" /></a>

Want to learn how to write PowerShell functions and script modules from a former winner of the advanced category in the scripting games and a multiyear recipient of both Microsoft’s MVP and SAPIEN Technologies’ MVP award? Then you'll definitely want to attend my “<a href="https://powershelldevopsglobalsummit2018.sched.com/event/CnMp/writing-award-winning-powershell-functions-and-script-modules" target="_blank" rel="noopener">Writing award winning PowerShell functions and script modules</a>” session at the <a href="https://powershell.org/summit/" target="_blank" rel="noopener">PowerShell + DevOps Global Summit 2018</a>.

µ