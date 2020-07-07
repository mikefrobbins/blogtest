---
ID: 11345
post_title: 'PowerShell Advanced Functions: Can we build them better? With parameter validation, yes we can!'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/03/31/powershell-advanced-functions-can-we-build-them-better-with-parameter-validation-yes-we-can/
published: true
post_date: 2015-03-31 10:00:36
---
This blog article is the second in a series of blog articles that is being written on PowerShell advanced functions for PowerShell blogging week. If you haven't already read the first article in this series: <em>"<a href="http://www.lazywinadmin.com/2015/03/standard-and-advanced-powershell.html" target="_blank">Standard and Advanced PowerShell functions</a>"</em> written by <a href="http://twitter.com/LazyWinAdm" target="_blank">PowerShell MVP Francois-Xavier Cat</a>, I recommend reading it also.

What is parameter validation? In PowerShell, parameter validation is the automated testing to validate the accuracy of parameter values passed to a command.

Why validate parameter input? The question should be, can your function complete successfully without valid input being provided? If not, parameter validation should be performed to catch problems early on and before your function performs any actions. There could also be security risks associated with accepting input that isn't validated.

In this first example, no parameter validation is being performed:
<pre class="lang:ps decode:true" title="Test-NoValidation">function Test-NoValidation {
    [CmdletBinding()]
    param (
        $FileName
    )
    Write-Output $FileName
}</pre>
This allows any number of values and any value including null, empty, or invalid file names to be provided for the <em>FileName</em> parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate1a.jpg"><img class="alignnone size-full wp-image-11348" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate1a.jpg" alt="param-validate1a" width="877" height="119" /></a>

There are several different parameter validation attributes that can be used to validate the values that are provided for parameter input.

<em>ValidateLength</em> is one of those attributes. It validates that the number of characters are within a specified range as shown in the following example where the value provided for the <em>FileName</em> parameter must be between one and twelve characters in length:
<pre class="lang:ps decode:true" title="Test-ValidateLength">function Test-ValidateLength {
    [CmdletBinding()]
    param (
        [ValidateLength(1,12)]
        [string]$FileName
    )
    Write-Output "$FileName is $($FileName.Length) characters long"
}</pre>
Typing the <em>FileName</em> variable as a [string] prevents more than one value from being provided for it as shown in the previous example.

Values outside the specified character length generate an error:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate2a.jpg"><img class="alignnone size-full wp-image-11350" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate2a.jpg" alt="param-validate2a" width="877" height="299" /></a>

<em>ValidateLength</em> probably isn't the best parameter validation attribute for validating something like a file name since it allows invalid file name characters to be specified.

<em>ValidatePattern</em> validates the input against a regular expression:
<pre class="lang:ps decode:true" title="Test-ValidatePattern">function Test-ValidatePattern {
    [CmdletBinding()]
    param (
        [ValidatePattern('^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$')]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
If the value doesn't match the regular expression, an error is generated:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate3a.jpg"><img class="alignnone size-full wp-image-11353" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate3a.jpg" alt="param-validate3a" width="877" height="190" /></a>

As you can see in the previous example, the error messages that <em>ValidatePattern</em> generates are cryptic unless you read regular expressions and since most people don't, I typically avoid using it. The same type of input validation can be performed using <em>ValidateScript</em>  while providing the user of your function with a meaningful error message.

<em>ValidateScript</em> uses a script to validate the value:
<pre class="lang:ps decode:true " title="Test-ValidateScript">function Test-ValidateScript {
    [CmdletBinding()]
    param (
        [ValidateScript({
            If ($_ -match '^(?!^(PRN|AUX|CLOCK\$|NUL|CON|COM\d|LPT\d|\..*)(\..+)?$)[^\x00-\x1f\\?*:\"";|/]+$') {
                $True
            }
            else {
                Throw "$_ is either not a valid filename or it is not recommended."
            }
        })]
        [string]$FileName
    )
    Write-Output $FileName
}</pre>
Notice the meaningful error message:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate4a.jpg"><img class="alignnone size-full wp-image-11354" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate4a.jpg" alt="param-validate4a" width="877" height="181" /></a>

<em>ValidateCount</em> limits the number of values that can be provided:
<pre class="lang:ps decode:true" title="Test-ValidateCount">function Test-ValidateCount {
    [CmdletBinding()]
    param (
        [ValidateCount(2,6)]
        [string[]]$ComputerName
    )
    Write-Output "The ComputerName array contains $($ComputerName.Count) items."
}</pre>
Typing the variable as a [string[]] allows multiple values to be provided.

Specifying too few or too many values generates an error:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate6a.jpg"><img class="alignnone size-full wp-image-11356" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate6a.jpg" alt="param-validate6a" width="877" height="298" /></a>

<em>ValidateRange</em> verifies the value is within a specific numeric range:
<pre class="lang:ps decode:true " title="Test-ValidateRange">function Test-ValidateRange {
    [CmdletBinding()]
    param (
        [ValidateRange(1582,9999)]
        [int]$Year
    )
    Write-Output "$Year is a valid Gregorian calendar year"
}</pre>
Verify the input is between 1582 and 9999:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate7b.jpg"><img class="alignnone size-full wp-image-11496" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate7b.jpg" alt="param-validate7b" width="877" height="178" /></a>

<em>ValidateSet</em> specifies a specific set of valid values:
<pre class="lang:ps decode:true" title="Test-ValidateSet">function Test-ValidateSet {
    [CmdletBinding()]
    param ( 
        [ValidateSet('CurrentUser','LocalMachine')]
        [string]$StoreLocation
    )
    Write-Output $StoreLocation
}</pre>
Beginning with PowerShell version 3, those values will tab expand in the PowerShell console and they'll show up in intellisense in the PowerShell ISE (Integrated Scripting Environment) and most third party products such as <a href="http://www.sapien.com/software/powershell_studio" target="_blank">SAPIEN PowerShell Studio</a>.

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate8a.jpg"><img class="alignnone size-full wp-image-11359" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate8a.jpg" alt="param-validate8a" width="877" height="177" /></a>

In the previous examples, the parameters weren't designated as being mandatory however. This means that they aren't required to be specified:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate9a.jpg"><img class="alignnone size-full wp-image-11360" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate9a.jpg" alt="param-validate9a" width="877" height="72" /></a>

Mandatory parameters require the user to provide a value:
<pre class="lang:ps decode:true " title="Test-ValidateSet">#Requires -Version 3.0
function Test-ValidateSet {
    [CmdletBinding()]
    param ( 
        [Parameter(Mandatory)]
        [ValidateSet('CurrentUser','LocalMachine')]
        [string]$StoreLocation
    )
    Write-Output $StoreLocation
}</pre>
If a mandatory parameter isn't specified, you're prompted for a value:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate10a.jpg"><img class="alignnone size-full wp-image-11361" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate10a.jpg" alt="param-validate10a" width="877" height="119" /></a>

Default values can't be used with mandatory parameters. If a default value is specified with a mandatory parameter and the parameter isn't specified when calling the function, you'll still be prompted for a value (the default value will never be used).

<em>ValidateNotNullOrEmpty</em> prevents null or empty values from being provided and default values can be used with this particular validation attribute:
<pre class="lang:ps decode:true " title="Test-NotNullOrEmpty">function Test-NotNullOrEmpty {
    [CmdletBinding()]
    param ( 
        [ValidateNotNullOrEmpty()]
        [string]$ComputerName = $env:COMPUTERNAME
    )
    Write-Output $ComputerName
}</pre>
The default value is used when the parameter isn't specified:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate11a.jpg"><img class="alignnone size-full wp-image-11362" src="http://mikefrobbins.com/wp-content/uploads/2015/03/param-validate11a.jpg" alt="param-validate11a" width="877" height="96" /></a>

I've demonstrated the more common parameter validation attributes in this blog article, to learn more see the <a href="https://technet.microsoft.com/en-us/library/hh847743.aspx" target="_blank">about_Functions_Advanced_Parameters</a> help topic:
<pre class="lang:ps decode:true">help about_Functions_Advanced_Parameters -ShowWindow</pre>
Be sure to keep an eye on the <a href="http://twitter.com/hashtag/psblogweek?f=realtime" target="_blank">#PSBlogWeek</a> hash tag on twitter and the <a href="http://twitter.com/mikefrobbins/lists/psblogweek" target="_blank">PSBlogWeek</a> twitter list that I've created to know when each of the additional articles in this series of blog articles are published throughout the remainder of the week.

Also, be sure to take a look at my blog article <em>"<a href="http://mikefrobbins.com/2015/03/26/the-mother-of-all-powershell-blogs-week-is-coming-next-week/" target="_blank">The mother of all PowerShell blogs week is coming next week!</a>"</em> that I published last week to learn who the bloggers are that are participating in PowerShell blogging week.

<hr />

<span style="text-decoration: underline;">Update - April 5, 2015</span>
Yesterday was the last day in the PowerShell blogging week series so I thought I would add direct links to all of the articles since it has now concluded.
<table width="844">
<tbody>
<tr>
<td width="223">Name</td>
<td width="621">PSBlogWeek Article</td>
</tr>
<tr>
<td>Francois-Xavier Cat</td>
<td><a href="http://www.lazywinadmin.com/2015/03/standard-and-advanced-powershell.html" target="_blank">Standard and Advanced PowerShell functions</a></td>
</tr>
<tr>
<td>Mike F Robbins</td>
<td><a href="http://mikefrobbins.com/2015/03/31/powershell-advanced-functions-can-we-build-them-better-with-parameter-validation-yes-we-can/" target="_blank">PowerShell Advanced Functions: Can we build them better? With parameter validation, yes we can!</a></td>
</tr>
<tr>
<td>Adam Bertram</td>
<td><a href="http://www.adamtheautomator.com/psbloggingweek-dynamic-parameters-and-parameter-validation/" target="_blank">#PSBlogWeek – Dynamic Parameters and Parameter Validation</a></td>
</tr>
<tr>
<td>Jeffery Hicks</td>
<td><a href="http://jdhitsolutions.com/blog/2015/04/powershell-blogging-week-supporting-whatif-and-confirm/" target="_blank">PowerShell Blogging Week: Supporting WhatIf and Confirm</a></td>
</tr>
<tr>
<td>June Blender</td>
<td><a href="http://www.sapien.com/blog/2015/04/03/advanced-help-for-advanced-functions/" target="_blank">Advanced Help for Advanced Functions – #PSBlogWeek</a></td>
</tr>
<tr>
<td>Boe Prox</td>
<td><a href="http://learn-powershell.net/2015/04/04/a-look-at-trycatch-in-powershell/" target="_blank">A Look at Try/Catch in PowerShell</a></td>
</tr>
</tbody>
</table>
These articles have been complied into a <a href="http://mikefrobbins.com/2015/04/17/free-ebook-on-powershell-advanced-functions/">Free eBook on PowerShell Advanced Functions</a>.

µ