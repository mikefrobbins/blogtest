---
ID: 17616
post_title: >
  PowerShell Tokenizer more Accurate than
  AST in Certain Scenarios
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/02/21/powershell-tokenizer-more-accurate-than-ast-in-certain-scenarios/
published: true
post_date: 2019-02-21 07:30:00
---
As many of you know, I've been working on some module building tools. One of the things I needed was to retrieve a list of PowerShell modules that each function required (a list of dependencies). This seemed simple enough through PowerShell's AST (Abstract Syntax Tree) as shown in the following example.
<pre class="lang:ps decode:true ">$File = 'U:\GitHub\PowerShell\MrToolkit\Public\Find-MrModuleUpdate.ps1'
$AST = [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$null, [ref]$null)
$AST.ScriptRequirements.RequiredModules.Name</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer3a.jpg"><img class="alignnone size-full wp-image-17620" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer3a.jpg" alt="" width="859" height="90" /></a>

The modules that are retrieved by the AST are simply the ones specified in a functions <a href="https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_requires" target="_blank" rel="noopener"><em>Requires</em> statement</a>. What if someone forgot to add a required module to the <em>Requires</em> statement? How could this be validated?
<blockquote><a href="https://mikefrobbins.com/wp-content/uploads/2019/02/lightbulb100.jpg"><img class="alignleft size-full wp-image-17634" src="https://mikefrobbins.com/wp-content/uploads/2019/02/lightbulb100.jpg" alt="" width="100" height="100" /></a>Light bulb moment: I'll retrieve a list of all the commands used in a PowerShell function using the AST and then determine the module they exist in using <em>Get-Command</em>. Sounds simple enough, right? Well, not so fast.</blockquote>
While I've written functions on top of the functionality shown in this blog article, I wanted to keep this as simple as possible and eliminate those functions as the source of problems.

First, I've set a variable named <em>File</em> to the path of a function of mine named <em>Start-MrAutoStoppedService</em> which is contained in a PS1 file by the same name. It can be found in <a href="https://github.com/mikefrobbins/PowerShell" target="_blank" rel="noopener">my PowerShell GitHub repo</a>.
<pre class="lang:ps decode:true ">$File = 'U:\GitHub\PowerShell\MrToolkit\Public\Start-MrAutoStoppedService.ps1'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer4a.jpg"><img class="alignnone size-full wp-image-17622" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer4a.jpg" alt="" width="859" height="55" /></a>

Now I'll retrieve a list of all the commands used in the specified function with the AST.
<pre class="lang:ps decode:true ">$AST = [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$null, [ref]$null)
$AST.FindAll({$args[0].GetType().Name -like 'CommandAst'}, $true) |
ForEach-Object {
    $_.CommandElements[0].Value
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer1a.jpg"><img class="alignnone size-full wp-image-17617" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer1a.jpg" alt="" width="859" height="189" /></a>

As you can see in the previous set of results, the AST thinks there's a command named "<em>State</em>", but that's actually part of a WMI filter.
<pre class="lang:ps mark:6 decode:true">PROCESS {
    $Params.ComputerName = $ComputerName

    Invoke-Command @Params {            
        $Services = Get-WmiObject -Class Win32_Service -Filter {
            State != 'Running' and StartMode = 'Auto'
        }
            
        foreach ($Service in $Services.Name) {
            Get-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\$Service" |
            Where-Object {$_.Start -eq 2 -and $_.DelayedAutoStart -ne 1} |
            Select-Object -Property @{label='ServiceName';expression={$_.PSChildName}} |
            Start-Service @Using:RemoteParams
        }            
    }
}</pre>
Using the tokenizer instead of the AST returns more accurate results excluding "<em>State</em>" as shown in the following example.
<pre class="lang:ps decode:true ">$Token = $null
$null = [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$Token, [ref]$null)
Write-Output ($Token | Where-Object {$_.TokenFlags -eq 'CommandName'}).Value</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer2a.jpg"><img class="alignnone size-full wp-image-17618" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer2a.jpg" alt="" width="859" height="153" /></a>

While I'll clean this up and turn it into a function, the following example shows the basic functionality to retrieve a list of required modules from a function based on the commands used within it instead of relying on someone to remember to add them to the <em>Requires</em> statement.
<pre class="lang:ps decode:true ">$Token = $null
$null = [System.Management.Automation.Language.Parser]::ParseFile($File, [ref]$Token, [ref]$null)
Write-Output ($Token | Where-Object {$_.TokenFlags -eq 'CommandName'}).Value |
Get-Command | Select-Object -ExpandProperty Source -Unique</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer5a.jpg"><img class="alignnone size-full wp-image-17624" src="https://mikefrobbins.com/wp-content/uploads/2019/02/ast-vs-tokenizer5a.jpg" alt="" width="859" height="130" /></a>

Maybe I'm missing something as far as the AST goes and maybe there's a way to retrieve an accurate list using it? Please post your questions, comments, and/or suggestions as a comment to this blog article.

µ