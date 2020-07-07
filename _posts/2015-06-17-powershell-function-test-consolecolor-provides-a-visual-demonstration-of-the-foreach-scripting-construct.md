---
ID: 11827
post_title: 'PowerShell function: Test-ConsoleColor provides a visual demonstration of the foreach scripting construct'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/06/17/powershell-function-test-consolecolor-provides-a-visual-demonstration-of-the-foreach-scripting-construct/
published: true
post_date: 2015-06-17 07:30:27
---
<em>Test-ConsoleColor</em> is a PowerShell function that I recently wrote to provide a visual demonstration of how to loop through a series of objects in PowerShell using the <a href="https://technet.microsoft.com/en-us/library/hh847816.aspx" target="_blank">foreach</a> scripting construct, not to be confused with the <em><a href="https://technet.microsoft.com/en-us/library/hh849731.aspx" target="_blank">ForEach-Object</a></em> cmdlet.
<pre class="lang:ps decode:true " title="Test-ConsoleColor">#Requires -Version 3.0 -Modules Pscx
function Test-ConsoleColor {

&lt;#
.SYNOPSIS
    Tests all the different color combinations for the PowerShell console.
 
.DESCRIPTION
    Test-ConsoleColor is a PowerShell function that by default iterates through
    all of the possible color combinations for the PowerShell console. The PowerShell
    Community Extensions Module is required by the function. 
 
.PARAMETER Color
    One or more colors that is part of the System.ConsoleColor enumeration. Run
    [Enum]::GetValues([System.ConsoleColor]) in PowerShell to see the possible values.
 
.PARAMETER Paragraphs
    The number of latin paragraphs to generate during each foreground color test.
 
.PARAMETER Milliseconds
    Specifies how long to wait between each iteration of color changes in milliseconds.
 
.EXAMPLE
     Test-ConsoleColor
 
.EXAMPLE
     Test-ConsoleColor -Color Red, Blue, Green
 
.EXAMPLE
     Test-ConsoleColor -Paragraphs 7

.EXAMPLE
     Test-ConsoleColor -Milliseconds 300

.EXAMPLE
     Test-ConsoleColor -Color Red, Green, Blue -Paragraphs 7 -Milliseconds 300
 
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
        [ValidateNotNullOrEmpty()]
        [System.ConsoleColor[]]$Color = [System.Enum]::GetValues([System.ConsoleColor]),
        
        [ValidateNotNullOrEmpty()]
        [int]$Paragraphs = 5,
        
        [ValidateNotNullOrEmpty()]
        [int]$Milliseconds = 100
    )

    if ($Host.Name -ne 'ConsoleHost') {
        Throw 'This function can only be run in the PowerShell Console.'
    }
        
    $BG = [System.Console]::BackgroundColor
    $FG = [System.Console]::ForegroundColor
    $Title = [System.Console]::Title

    foreach ($BGColor in $Color) {        
        [System.Console]::BackgroundColor = $BGColor
        Clear-Host
        
        foreach ($FGColor in $Color) {
            [System.Console]::ForegroundColor = $FGColor
            [System.Console]::Title = "ForegroundColor: $FGColor / BackgroundColor: $BGColor"
            Clear-Host

            Write-Verbose -Message "Foreground Color is: $FGColor"
            Write-Verbose -Message "Background Color is $BGColor"

            Get-LoremIpsum -Length $Paragraphs
            Start-Sleep -Milliseconds $Milliseconds
        }
    }

    [System.Console]::BackgroundColor = $BG
    [System.Console]::ForegroundColor = $FG
    [System.Console]::Title = $Title
    Clear-Host

}</pre>
http://youtu.be/bS3DRQNZNGI

The <em>Test-ConsoleColor</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ