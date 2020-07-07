---
ID: 14817
post_title: >
  Locations for Comment-based Help in
  PowerShell Functions
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/12/07/locations-for-comment-based-help-in-powershell-functions/
published: true
post_date: 2016-12-07 07:30:35
---
One of the first things you'll learn when beginning with PowerShell is how to use the help system. When working from the PowerShell console, I use the help function and omit the Name parameter since it's positional and then specify the name of the cmdlet that I'm looking for help on as shown in the following example.
<pre class="lang:ps decode:true ">help Get-HotFix</pre>
<img class="alignnone size-full wp-image-14818" src="http://mikefrobbins.com/wp-content/uploads/2016/12/help-locations1a.png" alt="help-locations1a" width="859" height="462" />

This same type of standardized help can be added to your PowerShell functions and scripts which makes it easy for others to learn how to use them. It's called comment based help and it can be placed in one of three locations but there are some caveats to two of the options.

Me personally, I prefer to write my code in such a way that it works the same way no matter what the scenario is and it operates trouble free even when someone else needs to modify it. This is why I personally choose to place my comment based help just inside the function declaration:
<pre class="lang:ps decode:true ">function Test-HelpLocation {

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
    param (
        $ComputerName = $env:COMPUTERNAME
    )

    Write-Output "Does Something to $ComputerName"

}</pre>
If someone downloads a copy of one of my functions from GitHub and accidentally places multiple carriage returns before or after the comment based help, it still works without issue. I've also found that the average PowerShell script module creator places multiple functions inside of a script module file (PSM1) and this options allows anyone to clearly be able to determine what help goes with which function and it also allows the function and its help to be collapsed together.

Another option is to place the comment based help prior to the function declaration:
<pre class="lang:ps decode:true ">&lt;#
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

function Test-HelpLocation2 {
    [CmdletBinding()]
    param (
        $ComputerName = $env:COMPUTERNAME
    )

    Write-Output "Does Something to $ComputerName"
}</pre>
There's two reasons I don't like this option. If more than one empty line exists between the help and the function, it simply won't work as PowerShell assumes the help is intended for the script itself and not the function. This option also doesn't allow the help to be collapsed with the function so if you happen to place more than one function inside a PSM1 script module file, it's less than optimal (at least in my opinion).

The third option is to place the help at the bottom of the function just inside the closing curly brace for the function:
<pre class="lang:ps decode:true">function Test-HelpLocation3 {
    [CmdletBinding()]
    param (
        $ComputerName = $env:COMPUTERNAME
    )

    Write-Output "Does Something to $ComputerName"    

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

}</pre>
While this location is valid for functions, it isn't valid for scripts when they're digitally signed which means my code couldn't be written this way in all scenarios so that particular option is out since I want all of my code written in a standardized format.

Ultimately where you decide to place comment based help is a personal preference and you should follow your organizations standards or the project creators standards if you're contributing to someone else's code. See <a href="https://msdn.microsoft.com/en-us/powershell/reference/5.1/microsoft.powershell.core/about/about_comment_based_help" target="_blank">the about_Comment_Based_Help article on MSDN</a> for more information on comment based help.

Know of any additional reasons why you would use one option over another? Feel free to post them via a comment to this blog article.

µ