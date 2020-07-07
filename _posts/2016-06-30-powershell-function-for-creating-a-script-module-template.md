---
ID: 14139
post_title: >
  PowerShell function for creating a
  script module template
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/06/30/powershell-function-for-creating-a-script-module-template/
published: true
post_date: 2016-06-30 07:30:50
---
I'm curious to know what process others use to create new PowerShell script modules?

Since the initial process of creating a PowerShell script module seems redundant and tedious, I decided to create a function that creates a template for new script modules that I create which includes creating both the script module PSM1 and manifest PSD1 files and filling in the information that I would normally include:
<pre class="lang:ps decode:true " title="New-MrScriptModule">function New-MrScriptModule {

&lt;#
.SYNOPSIS
    Creates a new PowerShell script module in the specified location.
 
.DESCRIPTION
    New-MrScriptModule is an advanced function that creates a new PowerShell script module in the
    specified location including creating the module folder and both the PSM1 script module file
    and PSD1 module manifest.
 
.PARAMETER Name
    Name of the script module.

.PARAMETER Path
    Parent path of the location to create the script module in. This location must already exist.

.PARAMETER Author
    Specifies the module author.

.PARAMETER CompanyName
    Identifies the company or vendor who created the module.

.PARAMETER Description
    Describes the contents of the module.

.PARAMETER PowerShellVersion
    Specifies the minimum version of Windows PowerShell that will work with this module. For example,
    you can enter 3.0, 4.0, or 5.0 as the value of this parameter.
 
.EXAMPLE
     New-MrScriptModule -Name MyModuleName -Path "$env:ProgramFiles\WindowsPowerShell\Modules" -Author 'Mike F Robbins' -CompanyName mikefrobbins.com -Description 'Brief description of my PowerShell module' -PowerShellVersion 3.0

.INPUTS
    None
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$Name,
        
        [ValidateScript({
          If (Test-Path -Path $_ -PathType Container) {
            $true
          }
          else {
            Throw "'$_' is not a valid directory."
          }
        })]
        [String]$Path,

        [Parameter(Mandatory)]
        [string]$Author,

        [Parameter(Mandatory)]
        [string]$CompanyName,

        [Parameter(Mandatory)]
        [string]$Description,

        [Parameter(Mandatory)]
        [string]$PowerShellVersion
    )

    New-Item -Path $Path -Name $Name -ItemType Directory | Out-Null
    Out-File -FilePath "$Path\$Name\$Name.psm1" -Encoding utf8
    Add-Content -Path "$Path\$Name\$Name.psm1" -Value '#Dot source all functions in all ps1 files located in the module folder
Get-ChildItem -Path $PSScriptRoot\*.ps1 -Exclude *.tests.ps1, *profile.ps1 |
ForEach-Object {
    . $_.FullName
}'
    New-ModuleManifest -Path "$Path\$Name\$Name.psd1" -RootModule $Name -Author $Author -Description $Description -CompanyName $CompanyName `
    -PowerShellVersion $PowerShellVersion -AliasesToExport $null -FunctionsToExport $null -VariablesToExport $null -CmdletsToExport $null
}</pre>
When the previous function is run, a folder with the specified module name is created along with the PSM1 and PSD1 files. The PSM1 is set to dot source any function that exists in the module's folder. I could have easily coded the specific user and all users paths for modules into the function, but I rarely develop new modules in either of those locations.
<pre class="lang:ps decode:true ">New-MrScriptModule -Name MyModuleName -Path "$env:ProgramFiles\WindowsPowerShell\Modules" -Author 'Mike F Robbins' -CompanyName mikefrobbins.com -Description 'Brief description of my PowerShell module' -PowerShellVersion 3.0</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/06/module-template1a.jpg"><img class="alignnone size-full wp-image-14140" src="http://mikefrobbins.com/wp-content/uploads/2016/06/module-template1a.jpg" alt="module-template1a" width="859" height="214" /></a>

The function shown in this blog article is a work in progress and can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. Feel free to fork the repository and submit pull requests if you can think of improvements that could be made to the code shown in this blog article.

µ