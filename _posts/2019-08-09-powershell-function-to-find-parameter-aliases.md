---
ID: 18037
post_title: >
  PowerShell Function to Find Parameter
  Aliases
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/08/09/powershell-function-to-find-parameter-aliases/
published: true
post_date: 2019-08-09 07:30:28
---
While sitting through <a href="https://twitter.com/JeffHicks" target="_blank" rel="noopener noreferrer">Jeff Hicks</a>' Advanced PowerShell Scripting Workshop at <a href="https://www.powershellchatt.com/" target="_blank" rel="noopener noreferrer">PowerShell on the River</a> in Chattanooga today, he mentioned there being a "Cn" alias for the <em>ComputerName</em> parameter of commands in PowerShell.

I've <a href="https://mikefrobbins.com/2012/08/30/finding-aliases-for-powershell-cmdlet-parameters/" target="_blank" rel="noopener noreferrer">previously written a one-liner to find parameter aliases</a> and at one time Microsoft had starting adding parameter aliases to the help for commands as referenced in that same blog article, but it appears that they've discontinued adding them to the help and removed the ones they previously added to it.

I thought I would polish the one-liner I had posted back in 2012 in the previously referenced blog article and turn it into a PowerShell function as shown in the following example.
<pre class="lang:ps decode:true ">#Requires -Version 3.0
function Find-MrParameterAlias {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory)]
        [string]$CmdletName,

        [ValidateNotNullOrEmpty()]
        [string]$ParameterName = '*'
    )
        
    (Get-Command -Name $CmdletName).parameters.values |
    Where-Object Name -like $ParameterName |
    Select-Object -Property Name, Aliases
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/08/find-param-alias1b.jpg"><img class="alignnone size-full wp-image-18039" src="https://mikefrobbins.com/wp-content/uploads/2019/08/find-param-alias1b.jpg" alt="" width="859" height="635" /></a>

The <em>Find-MrParameterAlias</em> function shown in this blog article can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener noreferrer">my PowerShell repository on GitHub</a>.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ