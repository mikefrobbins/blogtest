---
ID: 5552
post_title: >
  PowerShell v3 Where-Object Syntax
  Produces Different Results Than v2
  Syntax
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/08/powershell-v3-where-object-syntax-produces-different-results-than-v2-syntax/
published: true
post_date: 2012-10-08 07:30:10
---
I posted a blog yesterday about a PowerShell issue I was experiencing and the problem ended up being due to the new PowerShell version 3 <em>Where-Object</em> Syntax producing different results than the PowerShell version 2 <em>Where-Object</em> syntax.

Piping <em>Get-Volume</em> to <em>Where-Object</em> and filtering on the drive letter using the version 3 syntax appears to be case sensitive. It doesn't return results when using a lower case 'c':
<pre class="lang:ps decode:true">Get-Volume | where DriveLetter -eq 'C'
Get-Volume | where DriveLetter -eq 'c'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue2.jpg"><img class="alignnone size-full wp-image-5534" title="filtering-issue2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue2.jpg" width="485" height="147" /></a>

Using the PowerShell version 2 syntax with <em>Where-Object</em> does not appear to be case sensitive. Both upper and lower case letters return the same results:
<pre class="lang:ps decode:true">Get-Volume | where {$_.DriveLetter -eq 'C'}
Get-Volume | where {$_.DriveLetter -eq 'c'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue5.jpg"><img class="alignnone size-full wp-image-5549" title="filtering-issue5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue5.jpg" width="488" height="179" /></a>

Yeah, I know <em>Get-Volume</em> has a <em>DriveLetter</em> parameter to filter on this without having to use <em>Where-Object</em>. See <a href="http://mikefrobbins.com/2012/10/07/powershell-filtering-left-could-produce-different-results-than-filtering-with-where-object/" target="_blank">my previous blog</a> for more details on how I came to this conclusion.

Just an FYI, I've only tested this on Windows 8.

<span style="text-decoration: underline;">Update:</span>
Very strange. I've tried this on two other Windows 8 machines and neither one of them have this issue (they return the same results with either syntax) and I know at least one of them was installed with the same media. Further investigation to follow. I'll try loading PowerShell with the -noprofile option next to make sure nothing in my profile is causing an issue.

µ