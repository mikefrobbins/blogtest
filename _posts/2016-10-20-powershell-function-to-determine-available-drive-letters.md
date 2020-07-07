---
ID: 14648
post_title: >
  PowerShell Function to Determine
  Available Drive Letters
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/10/20/powershell-function-to-determine-available-drive-letters/
published: true
post_date: 2016-10-20 07:30:27
---
I've recently been working on some file server drive migrations. One of the steps I needed to perform was to change the drive letter of a current drive to a different available drive letter so I decided to write a PowerShell function to accomplish the task of determining what drive letters are available.

The <em>Get-MrAvailableDriveLetter</em> function shown in the following code example will run on systems with PowerShell version 2.0 or higher (I tested it all the way down to version 1.0, but that was a no go).
<pre class="lang:ps decode:true" title="Get-MrAvailableDriveLetter">#Requires -Version 2.0
function Get-MrAvailableDriveLetter {

&lt;#
.SYNOPSIS
    Returns one or more available drive letters.
 
.DESCRIPTION
    Get-MrAvailableDriveLetter is an advanced PowerShell function that returns one or more available
    drive letters depending on the specified parameters.
 
.PARAMETER ExcludeDriveLetter
    Drive letter(s) to exclude regardless if they're available or not. The default excludes drive letters
    A-F and Z.

.PARAMETER Random
    Return one or more available drive letters at random instead of the next available drive letter.

.PARAMETER All
    Return all available drive letters. The default is to only return the first available drive letter.
 
.EXAMPLE
     Get-MrAvailableDriveLetter

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter A-C

.EXAMPLE
     Get-MrAvailableDriveLetter -Random

.EXAMPLE
     Get-MrAvailableDriveLetter -All

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter A-C, M, Q, T, W-Z -All

.EXAMPLE
     Get-MrAvailableDriveLetter -Random -All

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter $null -Random -All

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
        [string[]]$ExcludeDriveLetter = ('A-F', 'Z'),

        [switch]$Random,

        [switch]$All
    )
    
    $Drives = Get-ChildItem -Path Function:[a-z]: -Name

    if ($ExcludeDriveLetter) {
        $Drives = $Drives -notmatch "[$($ExcludeDriveLetter -join ',')]"
    }

    if ($Random) {
        $Drives = $Drives | Get-Random -Count $Drives.Count
    }

    if (-not($All)) {
        
        foreach ($Drive in $Drives) {
            if (-not(Test-Path -Path $Drive)){
                return $Drive
            }
        }

    }
    else {
        Write-Output $Drives | Where-Object {-not(Test-Path -Path $_)}
    }

}</pre>
It contains a requires statement and comment based help as all well written functions that you plan to share should:
<pre class="lang:ps decode:true ">#Requires -Version 2.0
function Get-MrAvailableDriveLetter {

&lt;#
.SYNOPSIS
    Returns one or more available drive letters.
 
.DESCRIPTION
    Get-MrAvailableDriveLetter is an advanced PowerShell function that returns one or more available
    drive letters depending on the specified parameters.
 
.PARAMETER ExcludeDriveLetter
    Drive letter(s) to exclude regardless if they're available or not. The default excludes drive letters
    A-F and Z.

.PARAMETER Random
    Return one or more available drive letters at random instead of the next available drive letter.

.PARAMETER All
    Return all available drive letters. The default is to only return the first available drive letter.
 
.EXAMPLE
     Get-MrAvailableDriveLetter

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter A-C

.EXAMPLE
     Get-MrAvailableDriveLetter -Random

.EXAMPLE
     Get-MrAvailableDriveLetter -All

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter A-C, M, Q, T, W-Z -All

.EXAMPLE
     Get-MrAvailableDriveLetter -Random -All

.EXAMPLE
     Get-MrAvailableDriveLetter -ExcludeDriveLetter $null -Random -All

.INPUTS
    None
 
.OUTPUTS
    String
 
.NOTES
    Author:  Mike F Robbins
    Website: http://mikefrobbins.com
    Twitter: @mikefrobbins
#&gt;</pre>
Drive letters can be excluded via parameter input and by default A-F and Z are excluded. This can be negated by specifying $null or an empty string for the <em>ExcludeDriveLetter</em> parameter:
<pre class="lang:ps decode:true ">[CmdletBinding()]
param (
    [string[]]$ExcludeDriveLetter = ('A-F', 'Z'),</pre>
By default only the first available drive letter is returned and it can be randomized by specifying the <em>Random</em> parameter:
<pre class="lang:ps decode:true">    [switch]$Random,</pre>
All available drive letters can be returned by specifying the <em>All</em> parameter and they can also be returned in random order with the <em>Random</em> parameter:
<pre class="lang:ps decode:true">    [switch]$All
)</pre>
The drive letters themselves are obtained from the Function PSDrive:
<pre class="lang:ps decode:true">$Drives = Get-ChildItem -Path Function:[a-z]: -Name</pre>
The excluded drive letters are converted to a regular expression to minimize the amount of code that had to be written to exclude multiple drive letters and/or ranges of drive letters:
<pre class="lang:ps decode:true ">if ($ExcludeDriveLetter) {
    $Drives = $Drives -notmatch "[$($ExcludeDriveLetter -join ',')]"
}</pre>
If the Random parameter is specified, all of the available drive letters are randomized once the exclusions are removed. This allows a single line of code to be used regardless if a single or all results are desired to be returned in randomized order:
<pre class="lang:ps decode:true ">if ($Random) {
    $Drives = $Drives | Get-Random -Count $Drives.Count
}</pre>
Unless the <em>All</em> parameter is specified, the Return keyword is used to terminate execution once an available drive letter is found and tested to not be in use. Read more about the PowerShell Return keyword in <a href="http://mikefrobbins.com/2015/07/23/the-powershell-return-keyword/" target="_blank">a previous blog article that I've written</a>.
<pre class="lang:ps decode:true ">if (-not($All)) {
        
    foreach ($Drive in $Drives) {
        if (-not(Test-Path -Path $Drive)){
            return $Drive
        }
    }

}</pre>
If the <em>All</em> parameter is specified, all drive letters are tested and returned if they are not in use. This seemed to be the most efficient way to accomplish this task without testing all of them early on only to return a single result by default:
<pre class="lang:ps decode:true ">else {
    Write-Output $Drives | Where-Object {-not(Test-Path -Path $_)}
}</pre>
The function itself is simple to use:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/10/availabledriveletter1a.png"><img class="alignnone size-full wp-image-14654" src="http://mikefrobbins.com/wp-content/uploads/2016/10/availabledriveletter1a.png" alt="availabledriveletter1a" width="859" height="583" /></a>

The <em>Get-MrAvailableDriveLetter</em> function shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/PowerShell" target="_blank">my PowerShell repository on GitHub</a>.

µ