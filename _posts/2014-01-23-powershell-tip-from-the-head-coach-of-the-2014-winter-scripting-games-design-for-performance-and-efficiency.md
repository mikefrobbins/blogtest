---
ID: 8958
post_title: 'PowerShell Tip from the Head Coach of the 2014 Winter Scripting Games: Design for Performance and Efficiency!'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/01/23/powershell-tip-from-the-head-coach-of-the-2014-winter-scripting-games-design-for-performance-and-efficiency/
published: true
post_date: 2014-01-23 07:30:49
---
There are several concepts that come to mind when discussing the topic of designing your PowerShell commands for performance and efficiency, but in my opinion one of the items at the top of the list is "Filtering Left" which is what I'll be covering in this blog article.

First, let's start out by taking a look at an example of a simple one-liner command that's poorly written from a performance and efficiency standpoint:
<pre class="lang:ps decode:true">Get-Command | Where-Object modulename -eq ActiveDirectory | ForEach-Object name</pre>
When the previous command is run, all of the PowerShell cmdlets on your machine are returned by the first part of the command prior to the first pipe symbol (the part with the <em>Get-Command</em> cmdlet). They are then piped to the <em>Where-Object</em> cmdlet and then the ones that meet the condition in the where clause are piped to the <em>ForEach-Object</em> cmdlet.

On my Windows 8.1 machine which has the Remote Server Administration Tools installed as well as some other additional cmdlets, that would start out by returning 3696 cmdlets:
<pre class="lang:ps decode:true">(Get-Command).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4a1.png"><img class="alignnone size-full wp-image-8961" alt="posh-tip4a1" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4a1.png" width="700" height="73" /></a>

This is not exactly what happens, but think of it this way; Store all 3696 of those objects that are returned by the first part of that command in a variable named $Cmdlets:
<pre class="lang:ps decode:true">$Cmdlets = Get-Command</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4c.png"><img class="alignnone size-full wp-image-8962" alt="posh-tip4c" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4c.png" width="700" height="87" /></a>

Then filter them down to only the 147 that we're actually looking for and store those results in a variable named $ADCmdlets:
<pre class="lang:ps decode:true">$ADCmdlets = $Cmdlets | Where-Object modulename -eq ActiveDirectory</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4d.png"><img class="alignnone size-full wp-image-8963" alt="posh-tip4d" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4d.png" width="700" height="87" /></a>

Then send those results to <em>ForEach-Object</em> which iterates through each of those 147 items to retrieve the name for-each one. This step is also unnecessary:
<pre class="lang:ps decode:true">$ADCmdlets | ForEach-Object name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4e.png"><img class="alignnone size-full wp-image-8964" alt="posh-tip4e" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4e.png" width="700" height="105" /></a>

Instead of just telling you that this command is inefficient, I'll show you as well:
<pre class="lang:ps decode:true">Measure-Command {
   Get-Command | Where-Object modulename -eq ActiveDirectory | ForEach-Object name
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4f.png"><img class="alignnone size-full wp-image-8965" alt="posh-tip4f" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4f.png" width="700" height="289" /></a>

1.25 seconds doesn't seem too bad, but let's run the command 100 times to see how long that takes:
<pre class="lang:ps decode:true">1..100 | Measure-Command {
    Get-Command | Where-Object modulename -eq ActiveDirectory | ForEach-Object name
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4g.png"><img class="alignnone size-full wp-image-8966" alt="posh-tip4g" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4g.png" width="700" height="290" /></a>

It took almost 2 full minutes to run that same command 100 times.

Why was this command written this way? Where did it come from? I copied it off of someones else's blog who apparently didn't bother to "Read the Help". The original command also used cryptic aliases so it's not an exact copy. <a href="http://powershell.org/wp/2014/01/03/powershell-tip-1-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/" target="_blank">Tip #1 was "Read the Help"</a>.

Looking at the help for the <em>Get-Command</em> cmdlet, we can see it has a <em>-Module</em> parameter:
<pre class="lang:ps decode:true">Help Get-Command -Parameter Module</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4h.png"><img class="alignnone size-full wp-image-8967" alt="posh-tip4h" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4h.png" width="700" height="317" /></a>

By using the Module parameter,  you can filter as far to the left (Filter Left) as possible. In this particular scenario it's actually possible to filter before the first pipe symbol so you're only starting out with the exact results that you want (the 147 Active Directory cmdlets):
<pre class="lang:ps decode:true">(Get-Command -Module ActiveDirectory).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4i.png"><img class="alignnone size-full wp-image-8968" alt="posh-tip4i" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4i.png" width="700" height="71" /></a>

If you wanted just the name of the cmdlets, the command could be written this way:
<pre class="lang:ps decode:true">(Get-Command -Module ActiveDirectory).name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4j.png"><img class="alignnone size-full wp-image-8969" alt="posh-tip4j" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4j.png" width="877" height="128" /></a>

Or this way:
<pre class="lang:ps decode:true">Get-Command -Module ActiveDirectory | Select-Object -ExpandProperty Name</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4k.png"><img class="alignnone size-full wp-image-8970" alt="posh-tip4k" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4k.png" width="700" height="129" /></a>

This more efficient version of the command completes in about a tenth of the time as the inefficient one:
<pre class="lang:ps decode:true">Measure-Command {
   Get-Command -Module ActiveDirectory | Select-Object -ExpandProperty Name
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4l.png"><img class="alignnone size-full wp-image-8971" alt="posh-tip4l" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4l.png" width="700" height="288" /></a>

Running this more efficient command 100 times only takes 6 seconds. The inefficient one took almost 2 full minutes:
<pre class="lang:ps decode:true">1..100 | Measure-Command {
   Get-Command -Module ActiveDirectory | Select-Object -ExpandProperty Name
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4m.png"><img class="alignnone size-full wp-image-8972" alt="posh-tip4m" src="http://mikefrobbins.com/wp-content/uploads/2014/01/posh-tip4m.png" width="700" height="291" /></a>

It doesn't take a rocket scientist to figure out that 6 seconds is a lot more efficient than 2 full minutes :-)

µ