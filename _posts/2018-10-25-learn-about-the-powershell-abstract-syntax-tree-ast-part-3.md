---
ID: 17318
post_title: 'Learn about the PowerShell Abstract Syntax Tree (AST) &#8211; Part 3'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/
published: true
post_date: 2018-10-25 07:30:48
---
This blog article is the third in a series of learning about the PowerShell Abstract Syntax Tree (AST). Be sure to read the other two if you haven't already.
<ul>
 	<li><a href="https://mikefrobbins.com/2018/09/28/learning-about-the-powershell-abstract-syntax-tree-ast/" target="_blank" rel="noopener">Learning about the PowerShell Abstract Syntax Tree (AST)</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/24/learn-about-the-powershell-abstract-syntax-tree-ast-part-2/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 2</a></li>
 	<li><a href="https://mikefrobbins.com/2018/10/25/learn-about-the-powershell-abstract-syntax-tree-ast-part-3/" target="_blank" rel="noopener">Learn about the PowerShell Abstract Syntax Tree (AST) – Part 3</a></li>
</ul>
In this blog article, I'll be specifically focusing on finding the AST recursively.

I'll start off by storing the path to one of my functions in a variable.
<pre class="lang:ps decode:true">$FilePath = 'U:\GitHub\Hyper-V\MrHyperV\public\Get-MrVmHost.ps1'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast2a.jpg"><img class="alignnone size-full wp-image-17320" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast2a.jpg" alt="" width="859" height="56" /></a>

I'll store the AST in a variable and output what's in the variable just to confirm that it contains the AST.
<pre class="lang:ps decode:true ">$AST = [System.Management.Automation.Language.Parser]::ParseFile($FilePath, [ref]$null, [ref]$null)
$AST</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast3b.jpg"><img class="alignnone size-full wp-image-17322" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast3b.jpg" alt="" width="859" height="203" /></a>

Now to return the AST recursively. Although this command appears to return the same results based on the following screenshot, it actually returns much more.
<pre class="lang:ps decode:true">$AST.FindAll({$true}, $true)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast4a.jpg"><img class="alignnone size-full wp-image-17324" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast4a.jpg" alt="" width="859" height="193" /></a>

The following example gives a better idea of the results. Just returning the top level AST only returns one item, whereas querying the AST recursively returns 118 items, or 26 different unique types.
<pre class="lang:ps decode:true">($AST | Get-Member).TypeName | Sort-Object -Unique
($AST.FindAll({$true}, $true) | Get-Member).TypeName | Sort-Object -Unique</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast5a.jpg"><img class="alignnone size-full wp-image-17325" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast5a.jpg" alt="" width="859" height="395" /></a>

You'll probably be overwhelmed by all of the AST that's returned when querying it recursively. The solution is to narrow down the results. What are the AST type possibilities that can be used to narrow down the results?

A list of them can be retrieved by querying the <a href="https://docs.microsoft.com/en-us/dotnet/api/system.management.automation.language.arrayexpressionast" target="_blank" rel="noopener"><em>System.Management.Automation.Language.ArrayExpressionAst</em></a> .NET class. I ended up using a regular expression to cleanup the results and while I’ve seen others do this without a regex, they typically end up removing "<em>Expression"</em> from the list which makes it incomplete.
<pre class="lang:ps decode:true">([System.Management.Automation.Language.ArrayExpressionAst].Assembly.GetTypes() |
Where-Object {$_.Name.EndsWith('Ast') -and $_.Name -ne 'Ast'}).Name -replace '(?&lt;!^)ExpressionAst$|Ast$' |
Sort-Object -Unique</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast1a.jpg"><img class="alignnone size-full wp-image-17319" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast1a.jpg" alt="" width="859" height="560" /></a>

I'll use one of the items (Variable) from the previous list to return only the AST information for the variables used in my function.
<pre class="lang:ps decode:true ">$AST.FindAll({$args[0].GetType().Name -like "*Variable*Ast"}, $true)</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast6a.jpg"><img class="alignnone size-full wp-image-17328" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast6a.jpg" alt="" width="859" height="560" /></a>

Finally, I'll return a unique list of only the variables themselves that are used in the function.
<pre class="lang:ps decode:true ">$AST.FindAll({$args[0].GetType().Name -like "*Variable*Ast"}, $true) | Select-Object -Property Extent -Unique</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast7a.jpg"><img class="alignnone size-full wp-image-17329" src="https://mikefrobbins.com/wp-content/uploads/2018/10/part3-ast7a.jpg" alt="" width="859" height="201" /></a>

As you can see, there's a lot of power in using the AST.

In my next blog article, I'll share and demonstrate functions that I've created that use the AST to help automate my script module build process.

µ