---
ID: 15219
post_title: >
  Retrieve Information about your Favorite
  Podcast with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/05/11/retrieve-information-about-your-favorite-podcast-with-powershell/
published: true
post_date: 2017-05-11 07:30:27
---
This past weekend, I attended the 2017 Atlanta MVP Community Connection. While there, I met fellow Microsoft MVP <a href="https://twitter.com/theallenu" target="_blank" rel="noopener noreferrer">Allen Underwood</a> who is one of the co-host of the <a href="http://www.codingblocks.net/" target="_blank" rel="noopener noreferrer">{CodingBlocks}.NET</a> podcast. I listened to their podcast on my trip back home from Atlanta and later discovered that their podcast has an RSS feed for episodes.

A simple PowerShell one-liner can be used to retrieve information about each episode of their podcast:
<pre class="lang:ps decode:true ">Invoke-RestMethod -Uri http://www.codingblocks.net/podcast-feed.xml |
Select-Object -Property title, @{label='date';expression={($_.PubDate -as [datetime]).ToShortDateString()}}, link |
Out-GridView -Title 'Podcast Episodes' -OutputMode Multiple |
ForEach-Object {
    Start-Process -FilePath $_.link
}</pre>
Keep in mind that a PowerShell one-liner is one continuous pipeline and not necessarily a command that’s on one physical line. Not all commands that are on one physical line are one-liners.

The code shown in the previous example requires PowerShell version 3.0 or higher. It returns a list of all of their podcast episodes:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/05/podcast-rssfeed1b.png"><img class="alignnone size-full wp-image-15223" src="http://mikefrobbins.com/wp-content/uploads/2017/05/podcast-rssfeed1b.png" alt="" width="858" height="730" /></a>

I've gone ahead and selected the episodes I want to listen to as shown in blue in the previous example. Click OK and those episodes are automatically opened in your default web browser:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/05/podcast-rssfeed2a.png"><img class="alignnone size-full wp-image-15221" src="http://mikefrobbins.com/wp-content/uploads/2017/05/podcast-rssfeed2a.png" alt="" width="859" height="721" /></a>

Anytime you're going to be opening a web browser from PowerShell, I recommend running PowerShell non-elevated otherwise you're bypassing UAC (User Access Control) and asking for drive by malware (any application that's run from an elevated PowerShell session also runs elevated).

µ