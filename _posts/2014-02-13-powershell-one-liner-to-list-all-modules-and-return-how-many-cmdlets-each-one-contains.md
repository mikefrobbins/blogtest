---
ID: 9170
post_title: >
  PowerShell One-Liner to List All Modules
  and Return How Many Cmdlets Each One
  Contains
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/02/13/powershell-one-liner-to-list-all-modules-and-return-how-many-cmdlets-each-one-contains/
published: true
post_date: 2014-02-13 07:30:04
---
A big misconception when it comes to PowerShell one-liners is that they're one line of code as the name implies. That assumption is incorrect though because a one-liner can be written in one physical line of code such as in this example:
<pre class="lang:ps decode:true">Get-Module -ListAvailable | Import-Module -PassThru | ForEach-Object {Get-Command -Module $_} | Group-Object -Property ModuleName -NoElement | Sort-Object -Property Count -Descending</pre>
Or it can be written in multiple physical lines of code such as in the next example. How can that be? That's because a one-liner is one continuous pipeline.
<pre class="lang:ps decode:true">Get-Module -ListAvailable |
Import-Module -PassThru |
ForEach-Object {Get-Command -Module $_} |
Group-Object -Property ModuleName -NoElement |
Sort-Object -Property Count -Descending</pre>
One thing I also want you to notice is the "<em>-NoElement</em>" parameter that was used with the <em>Group-Object</em> cmdlet. Using that parameter keeps you from having to do additional unnecessary work such as piping the results to the <em>Select-Object</em> cmdlet or to one of the <em>Format</em> cmdlets to remove that property from your results.

<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/021314-1.png"><img class="alignnone size-full wp-image-9171" alt="021314-1" src="http://mikefrobbins.com/wp-content/uploads/2014/02/021314-1.png" width="700" height="1015" /></a>

Although the next command accomplishes the same task and it's on one physical line, it's not a one-liner since it contains a semicolon which breaks the pipeline (it's not one continuous pipeline):
<pre class="lang:ps decode:true">$Modules = Get-Module -ListAvailable | Import-Module; Get-Command -Module $Modules | Group-Object -Property ModuleName -NoElement | Sort-Object -Property Count -Descending</pre>
If I were only typing this one-liner interactively and didn't plan to save it as a script, the use of aliases and positional parameters would be acceptable, however in my opinion you should avoid the use of aliases and positional parameters when publishing content such as this blog article to avoid confusion and delay (as Sir Topham Hatt would say).
<h5><em>Note: The commands shown in this blog article return a list of all modules and the number of cmdlets that each of them contain that are installed on the machine they're being run on. If a module is located somewhere other than a path found in the $env:PSModulePath environment variable, it will not be returned by these commands unless it's explicitly imported. One module that I commonly work with that falls into this category is the Dell EqualLogic SAN PowerShell module.</em></h5>
µ