---
ID: 14168
post_title: >
  PowerShell function for creating a
  PowerShell function template
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/07/14/powershell-function-for-creating-a-powershell-function-template/
published: true
post_date: 2016-07-14 07:30:23
---
A couple of weeks ago I published a blog article "<a href="http://mikefrobbins.com/2016/06/30/powershell-function-for-creating-a-script-module-template/" target="_blank">PowerShell function for creating a script module template</a>" and I thought I would follow-up that article with the same type of function for creating a PowerShell function template. Instead of having to remember things like checking to make sure an approved verb is used, that a Pester test is created, and comment based help is entered in the right format, a template such as the one shown in the following code example can make your life much simpler.
<pre class="lang:ps decode:true " title="New-MrFunction">#Requires -Version 3.0 -Modules Pester
function New-MrFunction {

&lt;#
.SYNOPSIS
    Creates a new PowerShell function in the specified location.
 
.DESCRIPTION
    New-MrFunction is an advanced function that creates a new PowerShell function in the
    specified location including creating a Pester test for the new function.
 
.PARAMETER Name
    Name of the function.

.PARAMETER Path
    Path of the location where to create the function. This location must already exist.
 
.EXAMPLE
     New-MrFunction -Name Get-MrPSVersion -Path "$env:ProgramFiles\WindowsPowerShell\Modules\MyModule"

.INPUTS
    None
 
.OUTPUTS
    System.IO.FileInfo
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;

    [CmdletBinding()]
    [OutputType('System.IO.FileInfo')]
    param (
        [ValidateScript({
          If ((Get-Verb -Verb ($_ -replace '-.*$')).Verb) {
            $true
          }
          else {
            Throw "'$_' does NOT use an approved Verb."
          }
        })]
        [string]$Name,

        [ValidateScript({
          If (Test-Path -Path $_ -PathType Container) {
            $true
          }
          else {
            Throw "'$_' is not a valid directory."
          }
        })]
        [string]$Path
    )

    $FunctionPath = Join-Path -Path $Path -ChildPath "$Name.ps1"

    if (-not(Test-Path -Path $FunctionPath)) {
    
        New-Fixture -Path $Path -Name $Name
        Set-Content -Path $FunctionPath -Force -Value "#Requires -Version 3.0
function $($Name) {

&lt;#
.SYNOPSIS
    Brief synopsis about the function.
 
.DESCRIPTION
    Detailed explanation of the purpose of this function.
 
.PARAMETER Param1
    The purpose of param1.

.PARAMETER Param2
    The purpose of param2.
 
.EXAMPLE
     $($Name) -Param1 'Value1', 'Value2'

.EXAMPLE
     'Value1', 'Value2' | $($Name)

.EXAMPLE
     $($Name) -Param1 'Value1', 'Value2' -Param2 'Value'
 
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
    [OutputType('PSCustomObject')]
    param (
        [Parameter(Mandatory, 
                   ValueFromPipeline)]
        [string[]]`$Param1,

        [ValidateNotNullOrEmpty()]
        [string]`$Param2
    )

    BEGIN {
        #Used for prep. This code runs one time prior to processing items specified via pipeline input.
    }

    PROCESS {
        #This code runs one time for each item specified via pipeline input.

        foreach (`$Param in `$Param1) {
            #Use foreach scripting construct to make parameter input work the same as pipeline input (iterate through the specified items one at a time).
        }
    }

    END {
        #Used for cleanup. This code runs one time after all of the items specified via pipeline input are processed.
    }

}"
    
    }
    else {
        Write-Error -Message 'Unable to create function. Specified file already exists!'
    }    

}</pre>
When the function is run, two files are created. One for the function containing the template code and one for the Pester test:
<pre class="lang:ps decode:true ">New-MrFunction -Name Get-MrPSVersion -Path "$env:ProgramFiles\WindowsPowerShell\Modules\MyModule"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/07/function-template1a.jpg"><img class="alignnone size-full wp-image-14176" src="http://mikefrobbins.com/wp-content/uploads/2016/07/function-template1a.jpg" alt="function-template1a" width="859" height="190" /></a>

Sure, <a href="http://mikefrobbins.com/tag/script-analyzer/" target="_blank">PSScriptAnalyzer</a> or a <a href="http://mikefrobbins.com/tag/pester/" target="_blank">Pester</a> test could be used to make sure that things like an approved verb are used but both of those options are reactive instead of proactive. If I ended up using an unapproved verb initially until I used one of those tools, I may have hard coded it all over the place so why not treat things like using an approved verb kind of like parameter validation. Don't allow the user to continue until valid input (an approved verb in this case) is provided instead of finding the problem in hindsight.

The function shown in this blog article is a work in progress and can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. Feel free to fork the repository and submit pull requests if you can think of improvements that could be made to the code.

µ