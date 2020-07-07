---
ID: 11848
post_title: 'Walkthrough: An example of how I write PowerShell functions'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/06/19/walkthrough-an-example-of-how-i-write-powershell-functions/
published: true
post_date: 2015-06-19 07:30:39
---
A couple of days ago I posted a blog article titled "<a href="http://mikefrobbins.com/2015/06/17/powershell-function-test-consolecolor-provides-a-visual-demonstration-of-the-foreach-scripting-construct/" target="_blank">PowerShell function: <em>Test-ConsoleColor</em> provides a visual demonstration of the foreach scripting construct</a>" and today I thought I would walk you through that function step by step since it's what I consider to be a well written PowerShell function.

It starts out by using the <a href="https://technet.microsoft.com/en-us/library/hh847765.aspx" target="_blank">#Requires</a> statement to require at least PowerShell version 3 or it won't run. It also requires that the <a href="https://pscx.codeplex.com/" target="_blank">PowerShell Community Extensions</a> module be installed since it uses a function from that module and continuing without it only leads to errors:
<pre class="lang:ps decode:true">#Requires -Version 3.0 -Modules Pscx</pre>
The function is then declared using a <a href="https://msdn.microsoft.com/en-us/library/dd878270(v=vs.85).aspx#SD02" target="_blank">Pascal case name</a> that uses an <a href="https://msdn.microsoft.com/en-us/library/ms714428(v=vs.85).aspx" target="_blank">approved verb</a> along with a <a href="https://msdn.microsoft.com/en-us/library/dd878270(v=vs.85).aspx#SD01" target="_blank">singular noun</a>. <a href="https://technet.microsoft.com/en-us/library/hh847834.aspx" target="_blank">Comment based help</a> is provided just inside the function declaration. This isn't the only location where comment based help can be specified at, but it's my preferred location for it:
<pre class="lang:ps decode:true ">function Test-ConsoleColor {

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
#&gt;</pre>
This allows the function help to be viewed as it would be for any PowerShell command:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/06/test-consolecolor1b.jpg"><img class="alignnone size-full wp-image-11840" src="http://mikefrobbins.com/wp-content/uploads/2015/06/test-consolecolor1b.jpg" alt="test-consolecolor1b" width="877" height="403" /></a>

<a href="https://technet.microsoft.com/en-us/library/hh847872.aspx" target="_blank">CmdletBinding</a> is specified to turn this function into an advanced function. All of the parameters are specified using <a href="https://technet.microsoft.com/en-us/library/hh847743.aspx" target="_blank">parameter validation attributes</a>, the <em>Paragraphs</em> and <em>Milliseconds</em> parameters use data <a href="https://technet.microsoft.com/en-us/magazine/ff642464.aspx" target="_blank">type constraints</a>, and <a href="http://mikefrobbins.com/2015/06/15/using-a-net-enumeration-for-powershell-parameter-validation/" target="_blank">the <em>Color</em> parameter uses a .NET enumeration</a> to try to catch invalid input as early as possible in a standardized way. Default values are provided to make the function as easy to use as possible:
<pre class="lang:ps decode:true">[CmdletBinding()]
param (
    [ValidateNotNullOrEmpty()]
    [System.ConsoleColor[]]$Color = [System.Enum]::GetValues([System.ConsoleColor]),
        
    [ValidateNotNullOrEmpty()]
    [int]$Paragraphs = 5,
        
    [ValidateNotNullOrEmpty()]
    [int]$Milliseconds = 100
)</pre>
I only want this function to be run in the PowerShell console:
<pre class="lang:ps decode:true ">if ($Host.Name -ne 'ConsoleHost') {
    Throw 'This function can only be run in the PowerShell Console.'
}</pre>
Store the current console colors and title bar information in variables:
<pre class="lang:ps decode:true ">$BG = [System.Console]::BackgroundColor
$FG = [System.Console]::ForegroundColor
$Title = [System.Console]::Title
</pre>
The background color is set in one foreach loop and then a nested foreach loop sets the foreground color for each of the 16 available color options before the background color is changed again. It takes 256 total iterations to view all possible color combinations of the PowerShell console if you count both the foreach loops:
<pre class="lang:ps decode:true ">foreach ($BGColor in $Color) {        
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
}</pre>
Verbose output is also provided when the function is run with the <em>Verbose</em> parameter.

The colors and title bar are set back to the way they were before the function started using the variables that this information was previously stored in. The function declaration is closed with the last curly brace:
<pre class="lang:ps decode:true">    [System.Console]::BackgroundColor = $BG
    [System.Console]::ForegroundColor = $FG
    [System.Console]::Title = $Title
    Clear-Host

}</pre>
You could add things like <a href="https://technet.microsoft.com/en-us/library/hh847743.aspx" target="_blank">pipeline input</a> and <a href="https://technet.microsoft.com/en-us/library/hh847793.aspx" target="_blank">error handling</a> to improve it even further.

Remember to format your code for readability. Notice how the curly braces line up and the indentation of the code. Don't use positional parameters or aliases in scripts and functions or any code that you're sharing with others. Think about the next guy, it could be you.

I typically add my functions to a <a href="https://curah.microsoft.com/66835/writing-a-windows-powershell-module" target="_blank">script module</a> so I can simply call the function which auto-loads the module instead of having to manually locate and dot-source a .ps1 file. Note: Module auto-loading was introduced in PowerShell version 3.

Last but not least, you should place your code into some type of source control system such as <a href="https://github.com/" target="_blank">GitHub</a> or SAPIEN Technologies <a href="https://www.sapien.com/software/versionrecall" target="_blank">VersionRecall</a>. It doesn't matter if you're an IT Pro or a developer, you should be using source control &lt;period&gt;.

The <em>Test-ConsoleColor</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>. To learn more, download the "<a href="http://mikefrobbins.com/2015/04/17/free-ebook-on-powershell-advanced-functions/" target="_blank">Free eBook on PowerShell Advanced Functions</a>" that I contributed to along with several other PowerShell MVP's and/or enthusiasts.

µ