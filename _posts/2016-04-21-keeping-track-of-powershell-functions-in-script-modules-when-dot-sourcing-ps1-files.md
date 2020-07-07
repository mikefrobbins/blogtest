---
ID: 13856
post_title: >
  Keeping Track of PowerShell Functions in
  Script Modules when Dot-Sourcing PS1
  Files
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/04/21/keeping-track-of-powershell-functions-in-script-modules-when-dot-sourcing-ps1-files/
published: true
post_date: 2016-04-21 07:30:04
---
I'm picking up where I left off in a previous blog article "<em><a href="http://mikefrobbins.com/2016/04/14/write-dynamic-unit-tests-for-your-powershell-code-with-pester/" target="_blank">Write Dynamic Unit Tests for your PowerShell Code with Pester</a></em>". I'm using the dynamic <em>Test-MrFunctionsToExport</em> Pester test that I wrote about in that previous blog article. Currently, the test is failing for my MrToolkit module that's part of <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>:
<pre class="lang:ps decode:true ">Test-MrFunctionsToExport -ManifestPath .\GitHub\PowerShell\MrToolkit\MrToolkit.psd1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport1a.jpg"><img class="alignnone size-full wp-image-13863" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport1a.jpg" alt="functionstoexport1a" width="859" height="238" /></a>

Based on the previous results, I can easily determine that more functions exist in the module folder than are specified in the <em>FunctionsToExport</em> section of the module manifest. That may sound like a simple fix but once you have tons of PS1 files in the module folder and a similar number specified in the manifest, it becomes difficult to know what's missing so I've written a tool to generate a list of the function names:
<pre class="lang:ps decode:true ">function Get-MrFunctionsToExport {

&lt;#
.SYNOPSIS
    Returns a list of functions in the specified directory.
 
.DESCRIPTION
    Get-MrFunctionsToExport is an advanced function which returns a list of functions
    that are each contained in single quotes and each separated by a comma unless the
    simple parameter is specified in which case a simple list of the base file names
    for the functions is returned.
 
.PARAMETER Path
    Path to the folder where the functions are located.

.PARAMETER Exclude
    Pattern to exclude. By default profile scripts and Pester tests are excluded.

.PARAMETER Recurse
    Return function names from subdirectories in addition to the specified directory.

.PARAMETER Simple
    Return a simple list instead of a quoted comma separated list.

.EXAMPLE
    Get-MrFunctionsToExport -Path .\MrToolkit

.EXAMPLE
    Get-MrFunctionsToExport -Path .\MrToolkit -Simple

.INPUTS
    None
 
.OUTPUTS
    String
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [ValidateScript({
          If (Test-Path -Path $_ -PathType Container) {
            $True
          }
          else {
            Throw "'$_' is not a valid directory."
          }
        })]
        [string]$Path = (Get-Location),

        [string[]]$Exclude = ('*profile.ps1', '*.tests.ps1'),

        [switch]$Recurse,

        [switch]$Simple
    )

    $Params = @{
        Exclude = $Exclude
    }

    if ($PSBoundParameters.Recurse) {
        $Params.Recurse = $true
    }

    $results = Get-ChildItem -Path "$Path\*.ps1" @Params |
               Select-Object -ExpandProperty BaseName    
    
    if ((-not($PSBoundParameters.Simple)) -and $results) {
        $results = $results -join "', '"
        Write-Output "'$results'"
    }        
    elseif ($results) {
        Write-Output $results
    }
    
}</pre>
For those of you who didn't read the previous blog article that I referenced, I've moved to the model of placing each of my functions into a separate PS1 file and the file base name is the same as the function name. Each PS1 file is dot-sourced in the PSM1 file when the module is imported and a list of the function names must exist in the <em>FunctionsToExport</em> section of the module manifest (or exported via <em>Export-ModuleMember</em> in the PSM1 file).

I'll use this tool to generate a list of function names. If nothing is specified in the <em>FormatsToProcess</em> or <em>TypesToProcess</em> section of the module manifest, it can be updated programmatically using the <em>Update-ModuleManifest</em> cmdlet. Unfortunately <a href="https://windowsserver.uservoice.com/forums/301869-powershell/suggestions/12479136--bug-update-modulemanifest-fails-if-typestoproces" target="_blank">a known error</a> is generated as shown in the following example if either or both of these are specified:
<pre class="lang:ps decode:true ">Update-ModuleManifest -Path .\GitHub\PowerShell\MrToolkit\MrToolkit.psd1 -FunctionsToExport (Get-MrFunctionsToExport -Path .\GitHub\PowerShell\MrToolkit\ -Simple)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport2a.jpg"><img class="alignnone size-full wp-image-13865" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport2a.jpg" alt="functionstoexport2a" width="859" height="226" /></a>

<span style="color: #ff0000;"><em>Update-ModuleManifest : Cannot update the manifest file '.\GitHub\PowerShell\MrToolkit\MrToolkit.psd1' because the</em></span>
<span style="color: #ff0000;"><em>manifest is not valid. Verify that the manifest file is valid, and then try again.'The member 'FormatsToProcess' in</em></span>
<span style="color: #ff0000;"><em>the module manifest is not valid: Cannot find path</em></span>
<span style="color: #ff0000;"><em>'C:\Users\mikefrobbins\AppData\Local\Temp\1707008812\GitHub\PowerShell\MrToolkit\MrToolkit.ps1xml' because it does not</em></span>
<span style="color: #ff0000;"><em>exist.. Verify that a valid value is specified for this field in the</em></span>
<span style="color: #ff0000;"><em>'C:\Users\mikefrobbins\AppData\Local\Temp\1707008812\NewManifest.psd1' file.'</em></span>
<span style="color: #ff0000;"><em>At line:1 char:1</em></span>
<span style="color: #ff0000;"><em>+ Update-ModuleManifest -Path .\GitHub\PowerShell\MrToolkit\MrToolkit.p ...</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidArgument: (System.Argument...rd errorRecord):ArgumentException) [Update-ModuleMan</em></span>
<span style="color: #ff0000;"><em> ifest], ArgumentException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : UpdateManifestFileFail,Update-ModuleManifest</em></span>

The sub-command used in the previous example creates a simple list of the function names:
<pre class="lang:ps decode:true ">Get-MrFunctionsToExport -Path .\GitHub\PowerShell\MrToolkit\ -Simple</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport5a.jpg"><img class="alignnone size-full wp-image-13871" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport5a.jpg" alt="functionstoexport5a" width="859" height="312" /></a>

Luckily, I've written the <em>Get-MrFunctionToExport</em> function so it generates a list in the proper format that can be pasted into the module manifest if you happen to run into a scenario such as this one where <em>Update-ModuleManifest</em> can't be used.
<pre class="lang:ps decode:true ">Get-MrFunctionsToExport -Path .\GitHub\PowerShell\MrToolkit\</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport3a.jpg"><img class="alignnone size-full wp-image-13868" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport3a.jpg" alt="functionstoexport3a" width="859" height="118" /></a>

The easiest way to copy the results of the previous command is to simply pipe it to clip.exe:
<pre class="lang:ps decode:true ">Get-MrFunctionsToExport -Path .\GitHub\PowerShell\MrToolkit\ | clip.exe</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport4a.jpg"><img class="alignnone size-full wp-image-13870" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport4a.jpg" alt="functionstoexport4a" width="859" height="60" /></a>

After pasting the results into the <em>FunctionsToExport</em> section of the module's manifest (PSD1 file), the Pester test runs without issue:
<pre class="lang:ps decode:true ">Test-MrFunctionsToExport -ManifestPath .\GitHub\PowerShell\MrToolkit\MrToolkit.psd1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport6a.jpg"><img class="alignnone size-full wp-image-13878" src="http://mikefrobbins.com/wp-content/uploads/2016/04/functionstoexport6a.jpg" alt="functionstoexport6a" width="859" height="117" /></a>

Keeping track of functions and making sure they're all specified in the <em>FunctionsToExport</em> section of the module manifest can be a annoying to maintain but it's a necessary evil if you want module auto-loading to work properly when placing the functions from your modules in separate PS1 files.

Why not make your life a little easier by creating tools to make building other tools easier?

µ