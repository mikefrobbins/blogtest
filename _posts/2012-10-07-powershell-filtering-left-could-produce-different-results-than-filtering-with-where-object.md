---
ID: 5532
post_title: 'PowerShell: Filtering Left Could Produce Different Results Than Filtering with Where-Object'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/07/powershell-filtering-left-could-produce-different-results-than-filtering-with-where-object/
published: true
post_date: 2012-10-07 17:30:30
---
While doing some testing on my Windows 8 machine today, I discovered an issue where Filtering Left could produce different results than filtering with the Where-Object cmdlet.

Nothing special here. Just using the <em>Get-Volume</em> cmldet and filtering left with its <em>DriveLetter</em> parameter. Using either an upper or lowercase 'C' returns the same results:
<pre class="lang:ps decode:true">Get-Volume -DriveLetter 'C'
Get-Volume -DriveLetter 'c'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue1.jpg"><img class="alignnone size-full wp-image-5533" title="filtering-issue1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue1.jpg" width="488" height="174" /></a>

Using the <em>Where-Object</em> cmdlet to do the filtering, while not a best practice when the filtering can be accomplished earlier in the pipeline, should return the same results or at least you would think it should. Using an upper case 'C' returns the same results, but using a lower case 'c' does not return any results:
<pre class="lang:ps decode:true">Get-Volume | where DriveLetter -eq 'C'
Get-Volume | where DriveLetter -eq 'c'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue2.jpg"><img class="alignnone size-full wp-image-5534" title="filtering-issue2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue2.jpg" width="485" height="147" /></a>

Looking at both the datatype for the <em>DriveLetter</em> parameter (input) and the type of object that the DriveLetter property (output) produces shows they're both [Char]. Based on this, I would expect what I'm inputing for the <em>DriveLetter</em> parameter and what the cmdlet is producing for its output to the pipeline for the DriveLetter property which is used for input by the <em>Where-Object</em> cmdlet to work consistently.
<pre class="lang:ps decode:true">help Get-Volume -Parameter DriveLetter
Get-Volume | Get-Member</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue3.jpg"><img class="alignnone size-full wp-image-5535" title="filtering-issue3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue3.jpg" width="513" height="432" /></a>

I'm not a developer and  a quick search on the Internet didn't clear up whether the [Char] data type in PowerShell is case sensitive or not. So it was time for a quick test to check:
<pre class="lang:ps decode:true">[char]$charTest = 'C'
$charTest
$charTest -eq 'C'
$charTest -eq 'c'
$charTest -eq 'D'
$charTest | gm</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue4.jpg"><img class="alignnone size-full wp-image-5536" title="filtering-issue4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue4.jpg" width="321" height="237" /></a>

I defined a new variable named $charTest that is a [Char] datatype. I verified what it contained, tested it against upper and lower case both of which returned True, tested it against another value which returned False, and finally verified that it was an object type of [Char] to to make sure it was the right data type. Based on this test, it doesn't appear that a [Char] datatype in PowerShell is case sensitive.

<span style="text-decoration: underline;">Update:</span>
This appears to be an issue with the new PowerShell version 3 syntax for the <em>Where-Object</em> cmdlet which I was using in the previous examples. Using the older PowerShell version 2 syntax for the <em>Where-Object</em> cmdlet returns the correct results:
<pre class="lang:ps decode:true">Get-Volume | where {$_.DriveLetter -eq 'C'}
Get-Volume | where {$_.DriveLetter -eq 'c'}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue5.jpg"><img class="alignnone size-full wp-image-5549" title="filtering-issue5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/filtering-issue5.jpg" width="488" height="179" /></a>

Be sure to read <a href="http://mikefrobbins.com/2012/10/08/powershell-v3-where-object-syntax-produces-different-results-than-v2-syntax/" target="_blank">my next blog</a> where I followed up on this issue and discovered it doesn't occur on two other Windows 8 machines that I tested it on. This is a very strange issue. Also be sure to read Peter Kriegel's comment on this blog article (I forgot all about implicit conversions of data types).

µ