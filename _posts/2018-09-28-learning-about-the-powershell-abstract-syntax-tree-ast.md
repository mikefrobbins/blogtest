---
ID: 17124
post_title: >
  Learning about the PowerShell Abstract
  Syntax Tree (AST)
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/
published: true
post_date: 2018-09-28 07:30:32
---
This week, I'll continue where I left off in my previous blog article "<a href="https://mikefrobbins.com/2018/09/21/powershell-script-module-design-philosophy/" target="_blank" rel="noopener">PowerShell Script Module Design Philosophy</a>".

Moving forward, the development versions of my PowerShell script modules will use a non-monolithic design where each function is dot-sourced from the PSM1 file. When I move them to production, I'll convert them to using a monolithic design where all functions reside in the PSM1 file. In development, each PS1 file uses a <em>Requires</em> statement which specifies the requirements from a PowerShell version and required modules standpoint. This is so the requirements are known if someone grabs individual functions instead the entire module from the development version on GitHub. This information needs to be consolidated and placed in the module manifest (PSD1 file) when transitioning to production. The <em>Requires</em> statement should be excluded when retrieving the content of the function itself before placing it in the PSM1 file.

Since I didn't want to resort to writing regular expressions and parsing text, I decided to use the Abstract Syntax Tree (AST) to extract the content from the functions that I needed. Beginning with PowerShell version 3.0, script blocks have an AST property.

First, I'll store the content of one of my functions in a variable.
<pre class="lang:ps decode:true ">$GetVmHost = Get-Content -Path U:\GitHub\Hyper-V\MrHyperV\public\Get-MrVmHost.ps1 -Raw</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property1a.jpg"><img class="alignnone size-full wp-image-17133" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property1a.jpg" alt="" width="859" height="60" /></a>

Converting it to a script block and piping it to <em>Get-Member</em> shows that it does indeed have an AST property.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property2a.jpg"><img class="alignnone size-full wp-image-17134" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property2a.jpg" alt="" width="806" height="262" /></a>
<blockquote><em>Get-Member</em> is used to perform recon (exploration) on commands in PowerShell. Any command that produces object based output can be piped to <em>Get-Member</em>.</blockquote>
I just kept piping to <em>Get-Member</em> to see what kind of information could be retrieved.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property3a.jpg"><img class="alignnone size-full wp-image-17135" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property3a.jpg" alt="" width="859" height="406" /></a>

The <em>ScriptRequirements</em> property returns the information needed about which PowerShell version is required. It also returns the required modules if any were required, which none are for this particular function.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property4a.jpg"><img class="alignnone size-full wp-image-17136" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property4a.jpg" alt="" width="859" height="201" /></a>

I kept working with the properties and drilling down into each of them until I determined that the <em>Ast.EndBlock.Extent.Text</em> property returns the content of the function without the <em>Requires</em> statement.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property5a.jpg"><img class="alignnone size-full wp-image-17137" src="https://mikefrobbins.com/wp-content/uploads/2018/09/ast-property5a.jpg" alt="" width="859" height="560" /></a>

That was easy enough, but who really wants to cast everything as a script block just to view the AST for it? If you do, I've created a function named <em>ConvertTo-MrScriptBlock</em> that's part of <a href="https://github.com/mikefrobbins/ModuleBuildTools" target="_blank" rel="noopener">my ModuleBuildTools repository on GitHub</a>. Otherwise, I'll be showing you an easier way to accomplish the same task in a future blog article.

Be sure to read the continuation to this blog article series:
<ul>
 	<li><a href="https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/" target="_blank" rel="noopener">Learning about the PowerShell Abstract Syntax Tree (AST)</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/24/learn-about-the-powershell-abstract-syntax-tree-ast-part-2/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 2</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 3</a></li>
</ul>
µ