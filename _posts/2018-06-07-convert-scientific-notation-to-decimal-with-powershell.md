---
ID: 16564
post_title: >
  Convert Scientific Notation to Decimal
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/06/07/convert-scientific-notation-to-decimal-with-powershell/
published: true
post_date: 2018-06-07 07:30:26
---
Have you ever run into a problem where the results from a PowerShell command are returned in scientific notation? I've recently been working with performance counters in PowerShell and I've run into several scenarios where this occurs such as the one shown in the following example.
<pre class="lang:ps decode:true">(Get-Counter -Counter '\PhysicalDisk(*c:)\Avg. Disk sec/Read' -OutVariable Results).CounterSamples</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation1b.png"><img class="alignnone size-full wp-image-16569" src="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation1b.png" alt="" width="859" height="129" /></a>

In addition to returning the results in the previous example, they were also stored in a variable so the same value could be used throughout this blog article.
<blockquote>Windows PowerShell version 5.1 is used in the examples shown in this blog article. The performance counter cmdlets do not exist in PowerShell Core.</blockquote>
Only the value for the performance counter itself is returned in the following example.
<pre class="lang:ps decode:true">$Results.CounterSamples.CookedValue</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation2b.png"><img class="alignnone size-full wp-image-16570" src="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation2b.png" alt="" width="859" height="68" /></a>

To convert the scientific notation value shown in the previous example to decimal, remove <em>"E-05"</em> from end of it and move the decimal point left the number of places specified by the notation. That would be five decimal places in this scenario. If the notation were positive, it would need to be moved to the right that many decimal places.

While you could write a complicated function to perform the conversion, the simplest way is to cast it as a decimal.
<pre class="lang:ps decode:true">$Results.CounterSamples.CookedValue -as [decimal]</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation3b.png"><img class="alignnone size-full wp-image-16571" src="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation3b.png" alt="" width="859" height="70" /></a>

A second way of casting it as a decimal is shown in the following example.
<pre class="lang:ps decode:true ">[decimal]$Results.CounterSamples.CookedValue</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation4a.png"><img class="alignnone size-full wp-image-16577" src="http://mikefrobbins.com/wp-content/uploads/2018/06/scientific-notation4a.png" alt="" width="859" height="66" /></a>

As referenced by Wikipedia, <em>"<a href="https://en.wikipedia.org/wiki/Scientific_notation" target="_blank" rel="noopener">Scientific Notation</a> is a way of expressing numbers that are too big or too small to be conveniently written in decimal form"</em>.

µ