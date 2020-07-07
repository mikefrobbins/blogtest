---
ID: 17551
post_title: >
  Unexpected Results when Comparing
  Different Datatypes in PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/02/14/unexpected-results-when-comparing-different-datatypes-in-powershell/
published: true
post_date: 2019-02-14 07:30:05
---
Earlier this week, I saw <a href="https://twitter.com/ctmcisco/status/1095060405179813888" target="_blank" rel="noopener">a tweet</a> from a fellow PowerShell community member who mentioned PowerShell was returning inaccurate results. The command shown in the tweet was similar to the one in the following example.
<pre class="lang:ps decode:true">Get-AppxPackage |
Where-Object -Property IsFramework -eq 'False' |
Select-Object -Property Name, SignatureKind, IsFramework</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim1a.jpg"><img class="alignnone size-full wp-image-17554" src="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim1a.jpg" alt="" width="859" height="230" /></a>

Why would results be returned where "<em>IsFramework</em>" is true when the command is filtering them down to only the ones that are false?

I knew exactly what the problem was because I've been bit by it more times than I care to remember. Piping the <em>Get-AppxPackage</em> command to <em>Get-Member</em> shows that the <em>IsFramework</em> property returns a Boolean and the results in the previous example where skewed because a string was being compared to a Boolean.
<pre class="lang:ps decode:true">Get-AppxPackage | Get-Member</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim2a.jpg"><img class="alignnone size-full wp-image-17555" src="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim2a.jpg" alt="" width="859" height="496" /></a>

Simply changing the comparison value in the <em>Where-Object</em> statement from the string "<em>False</em>" to the Boolean <em>$false</em> returns the expected results as shown in the following example.
<pre class="lang:ps decode:true">Get-AppxPackage |
Where-Object -Property IsFramework -eq $false |
Select-Object -Property Name, SignatureKind, IsFramework</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim3a.jpg"><img class="alignnone size-full wp-image-17556" src="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim3a.jpg" alt="" width="859" height="214" /></a>

Because of the way implicit datatype conversion is performed in PowerShell, your mileage may vary depending on which way the comparison is being perform as shown in the following example where "<em>False</em>" is equal to <em>$false</em>, but <em>$false</em> isn't equal to "<em>False</em>".
<pre class="lang:ps decode:true">'False' -eq $false
$false -eq 'False'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim4a.jpg"><img class="alignnone size-full wp-image-17557" src="https://mikefrobbins.com/wp-content/uploads/2019/02/datatype-victim4a.jpg" alt="" width="859" height="101" /></a>

Please post your questions, comments, and/or suggestions as a comment to this blog article.

µ