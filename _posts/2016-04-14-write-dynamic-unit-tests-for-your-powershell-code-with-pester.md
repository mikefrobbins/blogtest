---
ID: 13774
post_title: >
  Write Dynamic Unit Tests for your
  PowerShell Code with Pester
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/04/14/write-dynamic-unit-tests-for-your-powershell-code-with-pester/
published: true
post_date: 2016-04-14 07:30:21
---
I wrote a blog article on: "<em><a href="http://mikefrobbins.com/2016/01/14/powershell-script-module-design-placing-functions-directly-in-the-psm1-file-versus-dot-sourcing-separate-ps1-files/" target="_blank">PowerShell Script Module Design: Placing functions directly in the PSM1 file versus dot-sourcing separate PS1 files</a></em>" earlier this year and I've moved all of my PowerShell script modules to that design and while today's blog article isn't part of a series, that previous one is recommended reading so you're not lost when trying to understand what I'm attempting to accomplish.

Most unit tests that I've seen created with Pester for testing PowerShell code are very specific, the tests generally have hard coded values in them, and live in source control along with the code that they're designed to test. What if I wanted to create unit tests for each of my modules to make sure they meet a certain standard? What if I could write them generically so the same or similar test wasn't being written over and over again since creating redundant code sounds like a lot of technical debt and something I try to avoid when writing PowerShell code so why not apply the same principles when writing Pester tests?

As described in that previously referenced blog article, I've moved to separating all of my functions into separate PS1 files that are dot-sourced from a PSM1 script module file. All of the functions must be listed in the <em>FunctionsToExport</em> section of the module manifest file (or exported via <em>Export-ModuleMember</em> in the PSM1 file) otherwise module auto-loading won't work properly. In my module design scenario, the base file name of each PS1 file matches the corresponding function name so I simply need to create a Pester test to compare the exported functions to the list of those base file names. Sounds simple enough, right?

I started by writing a Pester test, then wrapping it inside a function along with adding parameterization so it could be called just like any other function, accept dynamic input, and provide the same type of output that would normally be seen with any Pester unit test. This is some of the bonus content that I showed last week at the end of one of my presentations at the <a href="http://powershellsummit.org" target="_blank">PowerShell and DevOps Global Summit 2016</a>.
<pre class="lang:ps decode:true ">#Requires -Version 3.0 -Modules Pester
function Test-MrFunctionsToExport {

&lt;#
.SYNOPSIS
    Tests that all functions in a module are being exported.
 
.DESCRIPTION
    Test-MrFunctionsToExport is an advanced function that runs a Pester test against
    one or more modules to validate that all functions are being properly exported.
 
.PARAMETER ManifestPath
    Path to the module manifest (PSD1) file for the modules(s) to test.

.EXAMPLE
    Test-MrFunctionsToExport -ManifestPath .\MyModuleManifest.psd1

.EXAMPLE
    Get-ChildItem -Path .\Modules -Include *.psd1 -Recurse | Test-MrFunctionsToExport

.INPUTS
    String
 
.OUTPUTS
    None
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    param (
        [Parameter(ValueFromPipeline)]
        [ValidateScript({
            Test-ModuleManifest -Path $_
        })]
        [string[]]$ManifestPath
    )
    
    PROCESS {
        foreach ($Manifest in $ManifestPath) {

            $ModuleInfo = Import-Module -Name $Manifest -Force -PassThru

            $PS1FileNames = Get-ChildItem -Path "$($ModuleInfo.ModuleBase)\*.ps1" -Exclude *tests.ps1, *profile.ps1 |
                            Select-Object -ExpandProperty BaseName

            $ExportedFunctions = Get-Command -Module $ModuleInfo.Name |
                                 Select-Object -ExpandProperty Name

            Describe "FunctionsToExport for PowerShell module '$($ModuleInfo.Name)'" {

                It 'Exports one function in the module manifest per PS1 file' {
                    $ModuleInfo.ExportedFunctions.Values.Name.Count |
                    Should Be $PS1FileNames.Count
                }

                It 'Exports functions with names that match the PS1 file base names' {
                    Compare-Object -ReferenceObject $ModuleInfo.ExportedFunctions.Values.Name -DifferenceObject $PS1FileNames |
                    Should BeNullOrEmpty
                }

                It 'Only exports functions listed in the module manifest' {
                    $ExportedFunctions.Count |
                    Should Be $ModuleInfo.ExportedFunctions.Values.Name.Count
                }

                It 'Contains the same function names as base file names' {
                    Compare-Object -ReferenceObject $PS1FileNames -DifferenceObject $ExportedFunctions |
                    Should BeNullOrEmpty
                }
        
            }
    
        }

    }

}</pre>
I'll run this test on my MrToolkit module that's part of <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. The funny thing is this particular function is part of that module so in a way, it's testing itself:
<pre class="lang:ps decode:true">Test-MrFunctionsToExport -ManifestPath .\GitHub\PowerShell\MrToolkit\MrToolkit.psd1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester1a.jpg" rel="attachment wp-att-13789"><img class="alignnone size-full wp-image-13789" src="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester1a.jpg" alt="dyn-pester1a" width="859" height="238" /></a>

Based on the previous results, the first test tells me that 21 functions should have been exported but only 20 were so the test failed. Based on the failure of the second and fourth test and success of the third one, it's easy to determine the problem is that more functions exist in the module directory than are specified in the manifest.

After updating the <em>FunctionsToExport</em> section in the module manifest file, the test completes successfully:
<pre class="lang:ps decode:true ">Test-MrFunctionsToExport -ManifestPath .\GitHub\PowerShell\MrToolkit\MrToolkit.psd1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester2a.jpg" rel="attachment wp-att-13791"><img class="alignnone size-full wp-image-13791" src="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester2a.jpg" alt="dyn-pester2a" width="859" height="120" /></a>

The <em>Test-MrFunctionsToExport</em> function can also be used to test multiple modules at the same time as in this scenario where I'll test all of the modules in my GitHub repositories where the repository starts with the letter "<em>A</em>". Notice that the <em>Test-MrFunctionsToExport</em> function accepts pipeline input:
<pre class="lang:ps decode:true">Get-ChildItem -Path U:\GitHub\a* -Include *.psd1 -Recurse | Test-MrFunctionsToExport</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester3a.jpg" rel="attachment wp-att-13793"><img class="alignnone size-full wp-image-13793" src="http://mikefrobbins.com/wp-content/uploads/2016/04/dyn-pester3a.jpg" alt="dyn-pester3a" width="859" height="178" /></a>

Separating functions into PS1 files and dot-sourcing them from a PSM1 file definitely adds more complexity than placing them directly inside a module's PSM1 file, but in my opinion the benefits outweigh the disadvantages.

I think of Pester tests as not only a form of beginning with the end in mind, but being intentional about determining if the desired end result is going to be produced by whatever tool I'm building with PowerShell.

<span style="font-size: 17px; line-height: 1.7em;">µ</span>