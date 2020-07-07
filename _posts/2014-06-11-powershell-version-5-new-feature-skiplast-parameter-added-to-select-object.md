---
ID: 10024
post_title: 'PowerShell Version 5 New Feature: SkipLast Parameter added to Select-Object'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/11/powershell-version-5-new-feature-skiplast-parameter-added-to-select-object/
published: true
post_date: 2014-06-11 07:30:09
---
I'm picking up where I left off in <a href="http://mikefrobbins.com/2014/06/10/powershell-version-5-new-feature-nowait-parameter-added-to-stop-service/" target="_blank">my last blog article</a> on the new features in the PowerShell version 5 preview. Today I be covering a new parameter, <em>SkipLast</em> that has been added to the <em>Select-Object</em> cmdlet.

I have a text file where I saved a list of the available packages in the default OneGet Chocolatey repository. There are a total of 1893 items in this file:
<pre class="lang:ps decode:true">(Get-Content -Path C:\demo\packages.txt).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-skiplast1.jpg"><img class="alignnone size-full wp-image-10025" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-skiplast1.jpg" alt="ps5-skiplast1" width="877" height="72" /></a>

I'll use the new <em>SkipLast</em> parameter to skip the last 1885 items:
<pre class="lang:ps decode:true">Get-Content -Path C:\demo\packages.txt | Select-Object -SkipLast 1885</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-skiplast2.jpg"><img class="alignnone size-full wp-image-10026" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-skiplast2.jpg" alt="ps5-skiplast2" width="877" height="243" /></a>
<p style="color: #333333;">This blog article has been written based on the preview version of PowerShell version 5 from <a href="http://blogs.msdn.com/b/powershell/archive/2014/05/14/windows-management-framework-5-0-preview-may-2014-is-now-available.aspx" target="_blank">the May 2014 release of the Windows Management Framework 5.0 preview</a> so what you've seen here is subject to change in the final release version.</p>
<p style="color: #333333;">This blog article is one of many that I've written and will be writing about the new features in PowerShell version 5. You can find my other blog articles on PowerShell version 5 <a href="http://mikefrobbins.com/tag/powershell-version-5/" target="_blank">here</a>.</p>
µ