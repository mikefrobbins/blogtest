---
ID: 17519
post_title: >
  Return a List of the Private Functions
  in a PowerShell Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/01/31/return-a-list-of-the-private-functions-in-a-powershell-module/
published: true
post_date: 2019-01-31 07:30:14
---
In my quest to build a PowerShell module to convert a non-monolithic PowerShell module from development to a monolithic one for production, I wanted some way to validate that all of the functions were indeed migrated. While I'm pointing my tools to the public and private sub-folders within my development module and that should get them all, how can you be sure especially when a module may not have any private functions?

Determining the publicly accessible functions for a module is easy enough with either <em>Get-Command:
</em>
<pre class="lang:ps decode:true">Get-Command -Module MrModuleBuildTools</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions1a.jpg"><img class="alignnone size-full wp-image-17520" src="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions1a.jpg" alt="" width="859" height="201" /></a>

Or <em>Get-Module:</em>
<pre class="lang:ps decode:true">(Get-Module -Name MrModuleBuildTools).ExportedCommands.Values</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions2a.jpg"><img class="alignnone size-full wp-image-17521" src="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions2a.jpg" alt="" width="859" height="202" /></a>

But what about getting a list of private functions? <a href="https://twitter.com/mikefrobbins/status/1089923841017683968" target="_blank" rel="noopener">I tweeted this out</a> and received a couple of responses for things I'd already tried, but that didn't work consistently. You could manually import the module's PSM1 file instead of its PSD1 file, but this could be inaccurate if <em>Export-ModuleMember</em> is used in the PSM1 file. Even if it isn't, if the module is non-monolithic and any of the PS1 files specify a <em>Requires</em> statement for other modules, those module's commands will show as being part of your module because it thinks they're nested modules.

The solution I found is that <em>Get-Module</em> has an <em>Invoke</em> method which can be used to run a command within the module's scope.
<pre class="lang:ps decode:true ">$Module= Get-Module -Name MrModuleBuildTools
$Module.Invoke({Get-Command -Module MrModuleBuildTools})</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions3a.jpg"><img class="alignnone size-full wp-image-17522" src="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions3a.jpg" alt="" width="859" height="264" /></a>

Comparing the difference in the two returns a list of only the private functions.
<pre class="lang:ps decode:true">$All = $Module.Invoke({Get-Command -Module MrModuleBuildTools})
$Exported = Get-Command -Module MrModuleBuildTools
Compare-Object -ReferenceObject $All -DifferenceObject $Exported |
Select-Object -ExpandProperty InputObject</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions4a.jpg"><img class="alignnone size-full wp-image-17523" src="https://mikefrobbins.com/wp-content/uploads/2019/01/list-private-functions4a.jpg" alt="" width="859" height="200" /></a>

Know of a better way to retrieve a list of all of the private functions in a PowerShell module? I'd love to hear your thoughts or suggestions on this subject via a comment to this blog article.

µ