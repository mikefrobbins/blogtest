---
ID: 16110
post_title: 'PowerShell Compare-Object doesn&#8217;t handle null values'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/01/08/powershell-compare-object-doesnt-handle-null-values/
published: true
post_date: 2018-01-08 19:01:29
---
I thought I'd run into a bug with the <em>Compare-Object</em> cmdlet in PowerShell version 5.1 earlier today.
<pre class="lang:ps decode:true">$DriveLetters = (Get-Volume).DriveLetter
$DriveLetters
Compare-Object -ReferenceObject $DriveLetters -DifferenceObject $DriveLetters</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null1a.jpg"><img class="alignnone size-full wp-image-16111" src="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null1a.jpg" alt="" width="859" height="213" /></a>

<span style="color: #ff0000;"><em>Compare-Object : Cannot bind argument to parameter 'ReferenceObject' because it is null.</em></span>
<span style="color: #ff0000;"><em>At line:1 char:33</em></span>
<span style="color: #ff0000;"><em>+ Compare-Object -ReferenceObject $DriveLetters -DifferenceObject $Driv ...</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidData: (:) [Compare-Object], ParameterBindingValidationException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Microsoft.PowerShell.Commands.CompareObjectCommand</em></span>

Running the same commands on a VM with PowerShell version 5.0 completed without issue so it initially appeared to be a problem with the <em>Compare-Object</em> cmdlet in PowerShell version 5.1.

The error was actually due to <em>Get-Volume</em> returning several results that didn't have a drive letter on the system running PowerShell version 5.1.
<pre class="lang:ps decode:true">Get-Volume</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null2a.jpg"><img class="alignnone size-full wp-image-16112" src="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null2a.jpg" alt="" width="859" height="188" /></a>

There just happened to be no drives without letters on the VM that the results were being compared to. In other words, all drives had drive letters on the comparison VM. This explains why it succeeded without error on that particular system.

Once the entries without drive letters were filtered out on the system running PowerShell version 5.1, the command completed successfully.
<pre class="lang:ps decode:true">$DriveLetters = (Get-Volume).Where({$_.DriveLetter}).DriveLetter
$DriveLetters
Compare-Object -ReferenceObject $DriveLetters -DifferenceObject $DriveLetters</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null3a.jpg"><img class="alignnone size-full wp-image-16113" src="http://mikefrobbins.com/wp-content/uploads/2018/01/diff-null3a.jpg" alt="" width="859" height="116" /></a>

I would like to thank both <a href="https://twitter.com/JeffHicks" target="_blank" rel="noopener">Jeff Hicks</a> and <a href="https://twitter.com/alexandair" target="_blank" rel="noopener">Aleksandar Nikolic</a> for their assistance in helping determine the source of the problem. I thought I'd share this problem because I'm sure it's something that others will run into.

µ