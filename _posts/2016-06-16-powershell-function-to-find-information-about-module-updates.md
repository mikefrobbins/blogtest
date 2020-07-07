---
ID: 14118
post_title: >
  PowerShell function to find information
  about module updates
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/06/16/powershell-function-to-find-information-about-module-updates/
published: true
post_date: 2016-06-16 07:30:22
---
I decided to update the one liner from <a href="http://mikefrobbins.com/2016/06/09/update-manually-installed-powershell-modules-from-the-powershell-gallery/" target="_blank">my blog article last week</a> and take it a step further by turning it into a reusable tool that displays information about module updates that are available regardless of where they were installed from.
<pre class="lang:ps decode:true " title="Find-MrModuleUpdate">#Requires -Version 3.0 -Modules PowerShellGet
function Find-MrModuleUpdate {

&lt;#
.SYNOPSIS
    Finds updates for installed modules from an online gallery that matches the specified criteria.
 
.DESCRIPTION
    Find-MrModuleUpdate is a PowerShell advanced function that finds updates from an online gallery for locally installed modules
    regardless of whether or not they were originally installed from an online gallery or from the same online gallery where the
    update is found. 
 
.PARAMETER Name
    Specifies the names of one or more modules to search for.

.PARAMETER Scope
    Specifies the search scope of the installed modules. The acceptable values for this parameter are: AllUsers and CurrentUser.
 
.EXAMPLE
     Find-MrModuleUpdate

.EXAMPLE
     Find-MrModuleUpdate -Name PSScriptAnalyzer, PSVersion

.EXAMPLE
     Find-MrModuleUpdate -Scope CurrentUser

.EXAMPLE
     Find-MrModuleUpdate -Name PSScriptAnalyzer, PSVersion -Scope CurrentUser
 
.INPUTS
    None
 
.OUTPUTS
    Mr.ModuleUpdate
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    [OutputType('Mr.ModuleUpdate')]
    param (
        [ValidateNotNullOrEmpty()]
        [string[]]$Name,

        [ValidateSet('AllUsers', 'CurrentUser')]
        [string]$Scope
    )

    $AllUsersPath = "$env:ProgramFiles\WindowsPowerShell\Modules\*"
    $CurrentUserPath = "$env:USERPROFILE\Documents\WindowsPowerShell\Modules\*"

    switch ($Scope) {
        'AllUsers' {$Path = $AllUsersPath; break}
        'CurrentUser' {$Path = $CurrentUserPath; break}
        Default {$Path = $AllUsersPath, $CurrentUserPath}
    }

    $Params = @{
        ListAvailable = $true
    }

    if ($PSBoundParameters.Name) {
        $Params.Name = $Name
    }

    $Modules = Get-Module @Params

    foreach ($p in $Path) {
    
        $ScopedModules = $Modules |
        Where-Object ModuleBase -like $p |
        Sort-Object -Property Name, Version -Descending |
        Get-Unique

        foreach ($Module in $ScopedModules) {
            
            Remove-Variable -Name InstallInfo -ErrorAction SilentlyContinue
            $Repo = Find-Module -Name $Module.Name -ErrorAction SilentlyContinue

            if ($Repo) {
                $Diff = Compare-Object -ReferenceObject $Module -DifferenceObject $Repo -Property Name, Version |
                        Where-Object SideIndicator -eq '=&gt;'

                if ($Diff) {
                    $PSGetModuleInfoPath = "$($Module.ModuleBase)\PSGetModuleInfo.xml"

                    if (Test-Path -Path $PSGetModuleInfoPath) {
                        $InstallInfo = Import-Clixml -Path $PSGetModuleInfoPath
                    }

                    switch ($Module.ModuleBase) {
                        {$_ -like $AllUsersPath} {$Location = 'AllUsers'; break}
                        {$_ -like $CurrentUserPath} {$Location = 'CurrentUser'; break}
                        Default {Throw 'An unexpected error has occured.'}
                    }

                    [pscustomobject]@{
                        Name = $Module.Name
                        InstalledVersion = $Module.Version
                        InstalledLocation = $Location
                        Repository = $Repo.Repository
                        RepositoryVersion = $Diff.Version
                        InstalledFromRepository = $InstallInfo.Repository
                        PSTypeName = 'Mr.ModuleUpdate'
                    }

                }

            }
        
        }
    
    }

}</pre>
<pre class="lang:ps decode:true ">Find-MrModuleUpdate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate1a.jpg"><img class="alignnone size-full wp-image-14120" src="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate1a.jpg" alt="find-mrmoduleupdate1a" width="859" height="202" /></a>

As shown in the previous results, the function lists modules that have updates available regardless of where they were originally installed from. It also lists the installed version and location along with the repository where the updated version resides and the updated version number. It also lists where the module was originally installed from by reading the contents of the hidden XML file in the module directory if it was installed using PowerShellGet.

You can narrow your search down to specific modules:
<pre class="lang:ps decode:true">Find-MrModuleUpdate -Name PSScriptAnalyzer, PSVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate2a.jpg"><img class="alignnone size-full wp-image-14128" src="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate2a.jpg" alt="find-mrmoduleupdate2a" width="859" height="144" /></a>

To a specific scope:
<pre class="lang:ps decode:true">Find-MrModuleUpdate -Scope CurrentUser</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate3a.jpg"><img class="alignnone size-full wp-image-14129" src="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate3a.jpg" alt="find-mrmoduleupdate3a" width="859" height="132" /></a>

Use a combination of specific modules and scope to narrow the search down even further:
<pre class="lang:ps decode:true ">Find-MrModuleUpdate -Name PSScriptAnalyzer, PSVersion -Scope AllUsers</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate4a.jpg"><img class="alignnone size-full wp-image-14130" src="http://mikefrobbins.com/wp-content/uploads/2016/06/find-mrmoduleupdate4a.jpg" alt="find-mrmoduleupdate4a" width="859" height="131" /></a>

The function also uses custom formatting to allow for table output with more than four columns by default without having to pipe to <em>Format-Table</em>. The relevant portion of the PS1XML file used for the custom formatting is shown in the code example below:
<pre class="lang:ps decode:true ">&lt;View&gt;
    &lt;Name&gt;Mr.ModuleUpdate&lt;/Name&gt;
    &lt;ViewSelectedBy&gt;
        &lt;TypeName&gt;Mr.ModuleUpdate&lt;/TypeName&gt;
    &lt;/ViewSelectedBy&gt;
    &lt;TableControl&gt;
        &lt;TableHeaders&gt;
                &lt;TableColumnHeader&gt;
                &lt;Width&gt;30&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;                   
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;16&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;17&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;10&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;17&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
            &lt;TableColumnHeader&gt;
                &lt;Width&gt;23&lt;/Width&gt;
            &lt;/TableColumnHeader&gt;
        &lt;/TableHeaders&gt;
        &lt;TableRowEntries&gt;
            &lt;TableRowEntry&gt;
                &lt;TableColumnItems&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;Name&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;InstalledVersion&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;InstalledLocation&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;Repository&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;RepositoryVersion&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                    &lt;TableColumnItem&gt;
                        &lt;PropertyName&gt;InstalledFromRepository&lt;/PropertyName&gt;
                    &lt;/TableColumnItem&gt;
                &lt;/TableColumnItems&gt;
            &lt;/TableRowEntry&gt;
            &lt;/TableRowEntries&gt;
    &lt;/TableControl&gt;
&lt;/View&gt;</pre>
The <em>Find-MrModuleUpdate</em> function shown in this blog article is part of my MrToolkit module which can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ