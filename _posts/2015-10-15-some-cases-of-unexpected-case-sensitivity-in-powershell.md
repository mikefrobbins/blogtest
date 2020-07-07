---
ID: 12563
post_title: >
  Some Cases of Unexpected Case
  Sensitivity in PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/10/15/some-cases-of-unexpected-case-sensitivity-in-powershell/
published: true
post_date: 2015-10-15 07:30:46
---
I'm sure that you've all heard that PowerShell is case insensitive, right? Most things in PowerShell are indeed case insensitive and what is case sensitive is normally expected behavior, but I've come across a number of things in PowerShell that are case sensitive that don't work as I would expect them to which are listed in this blog article.

One of the first things that I discovered in PowerShell that is case sensitive that you wouldn't have thought would be is the <em>Region</em> keyword that was introduced in PowerShell version 3:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive1a.jpg"><img class="alignnone size-full wp-image-12565" src="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive1a.jpg" alt="case-sensitive1a" width="244" height="264" /></a>

Notice that in the previous example, placing the entire <em>region</em> and <em>endregion</em> keywords in anything other than all lower case breaks the ability to expand and compress the region so the <em>region</em> keyword is definitely case sensitive.

While working with the <a href="https://github.com/PowerShell/xSQLServer/commits?author=mikefrobbins" target="_blank">xSQLServer DSC resource</a>, I discovered that it wouldn't work properly if a feature was specified in your DSC configuration in a case other than upper case. I determined this was due to the <em>contains</em> method being case sensitive:
<pre class="lang:ps decode:true ">$Features = 'SQLENGINE', 'SSMS', 'ADV_SSMS'
$Features.Contains('SQLENGINE')
$Features.Contains('SQLEngine')</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive2a.jpg"><img class="alignnone size-full wp-image-12566" src="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive2a.jpg" alt="case-sensitive2a" width="877" height="108" /></a>

There are two ways to solve this problem. Either convert what the user enters to the same case specified in your code or use the contains keyword instead:
<pre class="lang:ps decode:true ">$Features.Contains('SQLEngine'.ToUpper())
$Features -contains 'SQLEngine'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive3a.jpg"><img class="alignnone size-full wp-image-12568" src="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive3a.jpg" alt="case-sensitive3a" width="877" height="94" /></a>

Case sensitivity isn't exclusive to the contains method as most if not all of the methods that can be called are case sensitive as shown here where I'll use the replace method:
<pre class="lang:ps decode:true ">$Features.Replace('SQLEngine', 'SQL')
$Features.Replace('SQLENGINE', 'SQL')</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive4a.jpg"><img class="alignnone size-full wp-image-12570" src="http://mikefrobbins.com/wp-content/uploads/2015/10/case-sensitive4a.jpg" alt="case-sensitive4a" width="877" height="142" /></a>

Notice in the previous example that when I attempted to replace 'SQLEngine' with 'SQL', the replace did not work due to 'SQLEngine' not being specified in all upper case. When I did specify it in all upper case, the replace method did indeed replace the word 'SQLENGINE' with 'SQL'.

This certainly isn't an all inclusive list of everything that's case sensitive in PowerShell, but only the things that I've come across that are case sensitive that I would have thought wouldn't be. If you happen to know or find other things that are case sensitive in PowerShell that you don't think should be, post them as a comment to this blog article.

µ