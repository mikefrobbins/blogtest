---
ID: 9646
post_title: >
  Exploring the Find-Package Cmdlet from
  the PowerShell version 5 Preview OneGet
  Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/13/exploring-the-find-package-cmdlet-from-the-powershell-version-5-preview-oneget-module/
published: true
post_date: 2014-04-13 07:30:32
---
While presenting on the OneGet Module in the preview version of PowerShell version 5 for the <a href="http://mspsug.com/" target="_blank">Mississippi PowerShell User Group</a> last week I discovered a couple of things about the <em>Find-Package</em> cmdlet that I wanted to share.

The first thing is wildcards don't work with the name parameter:
<pre class="lang:ps decode:true">Find-Package -Name *Java*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-49-23.png"><img class="alignleft size-full wp-image-9647" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-49-23.png" alt="2014-04-12_20-49-23" width="877" height="71" /></a>

That's seems to be because they're already performing a wildcard search as I'll search for 'Java' in the following example and notice that none of the packages are that exact name. Some contain other words before the word Java, some contain words afterwards, and some contain words before and afterwards so searching for Java effectively searches for *Java*. You'll also notice the search was not case sensitive:
<pre class="lang:ps decode:true">Find-Package -Name Java</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-47-36.png"><img class="alignleft size-full wp-image-9648" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-47-36.png" alt="2014-04-12_20-47-36" width="877" height="231" /></a>

You may think that the previous search contains all of the results for packages with Java in their name? That assumption would be incorrect. Adding the <em>-MinimumVersion</em> parameter returns more results and you would think it would return fewer results:
<pre class="lang:ps decode:true">Find-Package -Name Java -MinimumVersion 7.0</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-57-08.png"><img class="alignleft size-full wp-image-9649" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-57-08.png" alt="2014-04-12_20-57-08" width="877" height="445" /></a>

You might think that you'll out smart it and specify all versions from .1 to 100 to return everything, but that won't return anything:
<pre class="lang:ps decode:true">Find-Package -Name Java -MinimumVersion .1 -MaximumVersion 100</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-58-51.png"><img class="alignleft size-full wp-image-9650" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-58-51.png" alt="2014-04-12_20-58-51" width="877" height="71" /></a>

I thought this may be because MinimumVersion might want an integer, but checking the help for that parameter shows it expects a string:
<pre class="lang:ps decode:true">help Find-Package -Parameter MinimumVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-01-14.png"><img class="alignleft size-full wp-image-9651" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-01-14.png" alt="2014-04-12_21-01-14" width="877" height="228" /></a>

Specifying 0 or even 0.1 returns what you would expect:
<pre class="lang:ps decode:true">Find-Package -Name Java -MinimumVersion 0 -MaximumVersion 100</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-03-43.png"><img class="alignleft size-full wp-image-9652" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-03-43.png" alt="2014-04-12_21-03-43" width="877" height="612" /></a>

There's an easier way to accomplish the task of returning all versions though, by using the <em>-AllVersions</em> parameter:
<pre class="lang:ps decode:true">Find-Package -Name Java -AllVersions</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-05-33.png"><img class="alignleft size-full wp-image-9653" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-05-33.png" alt="2014-04-12_21-05-33" width="877" height="611" /></a>

A few days ago I meantioned being overwheled if you ran the <em>Find-Package</em> cmdlet without any parameters. That returned 1751 packages:
<pre class="lang:ps decode:true">(Find-Package).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-12-10.png"><img class="alignleft size-full wp-image-9654" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-12-10.png" alt="2014-04-12_20-12-10" width="877" height="76" /></a>

Adding this newly discovered <em>-AllVersions</em> parameter returns 7691 packages:
<pre class="lang:ps decode:true">(Find-Package -AllVersions).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-13-24.png"><img class="alignleft size-full wp-image-9655" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-13-24.png" alt="2014-04-12_20-13-24" width="877" height="73" /></a>

There's a <em>-IncludePrereleaseVersions</em> parameter, but adding that parameter returned the same number of packages as the previous command:
<pre class="lang:ps decode:true">(Find-Package -AllVersions -AllowPrereleaseVersions).count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-14-28.png"><img class="alignleft size-full wp-image-9656" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_20-14-28.png" alt="2014-04-12_20-14-28" width="877" height="75" /></a>

There's also a <em>-Hint</em> parameter which is interesting but also a bit mysterious and I'm still working on figuring out this parameter as it's not as self explanatory as the others:
<pre class="lang:ps decode:true">Find-Package -Hint Oracle -AllVersions</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-27-11.png"><img class="alignnone size-full wp-image-9668" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-27-11.png" alt="2014-04-12_21-27-11" width="877" height="443" /></a>
<pre class="lang:ps decode:true"> Find-Package -Hint 'VideoLAN Organization'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-30-47.png"><img class="alignnone size-full wp-image-9670" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-12_21-30-47.png" alt="2014-04-12_21-30-47" width="877" height="228" /></a>

I've written several <a href="http://mikefrobbins.com/tag/oneget/" target="_blank">blog articles about the OneGet module</a> during the past week so be sure to take a look at them for more information about the new features in the preview version of Powershell version 5.

µ