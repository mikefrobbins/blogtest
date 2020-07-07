---
ID: 17268
post_title: 'Learn about the PowerShell Abstract Syntax Tree (AST) &#8211; Part 2'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/10/24/learn-about-the-powershell-abstract-syntax-tree-ast-part-2/
published: true
post_date: 2018-10-24 07:30:39
---
In my previous blog article a few weeks ago on "<a href="https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/" target="_blank" rel="noopener">Learning about the PowerShell Abstract Syntax Tree (AST)</a>", I mentioned there was an easier way to retrieve the AST so that you didn't have to cast everything to a script block. There are two .NET static methods, <em><a href="https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.language.parser.parsefile" target="_blank" rel="noopener">ParseFile</a></em> and <a href="https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.language.parser.parseinput" target="_blank" rel="noopener"><em>ParseInput</em></a>, that are part of the <a href="https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.language.parser" target="_blank" rel="noopener"><em>Parser</em> class</a> in the <em>System.Management.Automation.Language</em> namespace which can be used to retrieve the AST.

First, I’ll store the content of one of my functions in a variable.
<pre class="lang:ps decode:true">$Code = Get-Content -Path U:\GitHub\Hyper-V\MrHyperV\public\Get-MrVmHost.ps1 -Raw</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast1b-1.jpg"><img class="alignnone size-large wp-image-17291" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast1b.jpg" alt="" width="859" height="59" /></a>

The <em>ParseInput</em> static method can be used with the content of the function from the previous example to retrieve things like the function itself without the <em>Requires</em> statement (no Regular Expression needed).
<pre class="lang:ps decode:true">$Tokens = $null
$Errors = $null
[System.Management.Automation.Language.Parser]::ParseInput($Code, [ref]$Tokens, [ref]$Errors)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast2b.jpg"><img class="alignnone size-full wp-image-17292" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast2b.jpg" alt="" width="859" height="218" /></a>

Due to the way that .NET works, you'll first need to define the variable used for <em>Tokens</em> and <em>Errors</em> otherwise an error will be generated.
<pre class="lang:ps decode:true ">[System.Management.Automation.Language.Parser]::ParseInput($Code, [ref]$Tokens, [ref]$Errors)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast3b.jpg"><img class="alignnone size-full wp-image-17293" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast3b.jpg" alt="" width="859" height="141" /></a>

<span style="color: #ff0000;"><em>[ref] cannot be applied to a variable that does not exist.</em></span>
<span style="color: #ff0000;"><em>At line:1 char:1</em></span>
<span style="color: #ff0000;"><em>+ [System.Management.Automation.Language.Parser]::ParseInput($Code, [re ...</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em>+ CategoryInfo : InvalidOperation: (Tokens:VariablePath) [], RuntimeException</em></span>
<span style="color: #ff0000;"><em>+ FullyQualifiedErrorId : NonExistingVariableReference</em></span>

If you don't need the information contained in them, instead of setting a variable to <em>$null</em> and then using it, you can reduce the number of steps necessary by simply using <em>$null</em> for them when calling the .NET method.
<pre class="lang:ps decode:true">[System.Management.Automation.Language.Parser]::ParseInput($Code, [ref]$null, [ref]$null)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast4b.jpg"><img class="alignnone size-full wp-image-17294" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast4b.jpg" alt="" width="859" height="193" /></a>

One last tip before moving onto the <em>ParseFile</em> static method. If you're going to use the <em>Get-Content</em> cmdlet to retrieve the code for working with <em>ParseInput</em>, be sure to add its <em>Raw</em> parameter otherwise the formatting of the code in the results will be skewed as shown in the following example.
<pre class="lang:ps decode:true ">$Code = Get-Content -Path U:\GitHub\Hyper-V\MrHyperV\public\Get-MrVmHost.ps1
[System.Management.Automation.Language.Parser]::ParseInput($Code, [ref]$null, [ref]$null)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast5a.jpg"><img class="alignnone size-full wp-image-17297" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast5a.jpg" alt="" width="859" height="515" /></a>

If you're going to retrieve all of the content from a file similarly to what I've shown so far in this blog article, save yourself a step or two by simply using the <em>ParseFile</em> static method instead of <em>ParseInput</em>.
<pre class="lang:ps decode:true ">$FilePath = 'U:\GitHub\Hyper-V\MrHyperV\public\Get-MrVmHost.ps1'
[System.Management.Automation.Language.Parser]::ParseFile($FilePath, [ref]$null, [ref]$null)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast6a.jpg"><img class="alignnone size-full wp-image-17299" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast6a.jpg" alt="" width="859" height="206" /></a>

Consider storing the results in a variable while prototyping with this or any command. Remember that any command that produces object based output can be piped to <em>Get-Member</em> to determine its methods and properties.
<pre class="lang:ps decode:true ">$Results = [System.Management.Automation.Language.Parser]::ParseFile($FilePath, [ref]$null, [ref]$null)
$Results | Get-Member</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast7a.jpg"><img class="alignnone size-full wp-image-17302" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part2-ast7a.jpg" alt="" width="859" height="419" /></a>

Note that the <em>System.Management.Automation.Language</em> namespace only exists in Windows PowerShell version 3.0 or higher, although that shouldn't be a problem since <a href="https://blogs.msdn.microsoft.com/powershell/2017/08/24/windows-powershell-2-0-deprecation/" target="_blank" rel="noopener">Windows PowerShell version 2.0 is deprecated</a>. All examples shown in this blog article were tested on Windows 10 Enterprise Edition version 1809 with both Windows PowerShell 5.1 and <a href="https://blogs.msdn.microsoft.com/powershell/2018/09/13/announcing-powershell-core-6-1/" target="_blank" rel="noopener">PowerShell Core 6.1</a> (PowerShell Core installs side by side on Windows based systems).

The examples shown in this blog article only return the top level AST. In Part 3 of this blog article series, I'll dive into searching for AST instances recursively.
<ul>
 	<li><a href="https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/" target="_blank" rel="noopener">Learning about the PowerShell Abstract Syntax Tree (AST)</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/24/learn-about-the-powershell-abstract-syntax-tree-ast-part-2/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 2</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 3</a></li>
</ul>
µ