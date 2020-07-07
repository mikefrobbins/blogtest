---
ID: 18232
post_title: >
  Setting Dependencies on the Azure
  PowerShell Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/01/28/setting-dependencies-on-the-azure-powershell-module/
published: true
post_date: 2020-01-28 07:30:32
---
I recently saw <a href="https://twitter.com/oising/status/1220750292263804928" target="_blank" rel="noopener noreferrer">a tweet</a> from <a href="https://twitter.com/Jaykul" target="_blank" rel="noopener noreferrer">Joel Bennett</a> about the <a href="https://www.powershellgallery.com/packages/Az/" target="_blank" rel="noopener noreferrer">Az (Azure) PowerShell module</a> being nothing more than an empty module that imports all of the modules for each Azure product.

<a href="https://twitter.com/oising/status/1220750292263804928" target="_blank" rel="noopener noreferrer"><img class="alignnone size-full wp-image-18233" src="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting1a.jpg" alt="" width="600" height="179" /></a>

I decided to investigate.
<pre class="lang:ps decode:true">Get-Content -Path (Get-Module -Name az).Path | Select-String -SimpleMatch 'Import-Module'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting2a.jpg"><img class="alignnone size-full wp-image-18237" src="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting2a.jpg" alt="" width="859" height="867" /></a>

Joel's statement is 100% accurate.

<hr />

<blockquote>Off-Topic: The searched for term is highlighted in each result when the previous command is run in PowerShell 7.</blockquote>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting4b.jpg"><img class="alignnone size-full wp-image-18243" src="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting4b.jpg" alt="" width="979" height="962" /></a>

<hr />

One thing I don't like about the Az module is the <em>Import-Module</em> statements use the <em>RequiredVersion</em> parameter.

This means that even though I've updated the Az.Compute module to version 3.4.0, it's going to load version 3.3.0 if I import the Az module. Without the <em>RequiredVersion</em> parameter, it would simply import the latest one.
<pre class="lang:ps decode:true">Get-Module -Name Az.Compute -ListAvailable</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting3a.jpg"><img class="alignnone size-full wp-image-18238" src="https://mikefrobbins.com/wp-content/uploads/2020/01/az-module-nesting3a.jpg" alt="" width="859" height="310" /></a>

While I guess this is an easy way to load all of the individual Azure PowerShell modules at once. If you only need some of them, you might be better off loading them individually. If you're setting some type of dependency, I would definitely recommend specifying the individual modules instead of the Az one. As Joel said in his tweet, "<em>Require what you actually need</em>". That will also give you better control over which version of the individual modules you're relying on.

If you haven't already, I'd recommend reading through <a href="https://twitter.com/oising/status/1220750292263804928" target="_blank" rel="noopener noreferrer">the twitter thread</a> that spurred this blog article. It contains some interesting comments.

µ