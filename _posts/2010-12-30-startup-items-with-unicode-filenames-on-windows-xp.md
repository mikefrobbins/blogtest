---
ID: 1553
post_title: >
  Startup Items with Unicode Filenames on
  Windows XP
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/12/30/startup-items-with-unicode-filenames-on-windows-xp/
published: true
post_date: 2010-12-30 05:30:15
---
Several end user personal computers that I've looked at recently have had some strange file names showing up in their startup routine. Here’s a look at what Msconfig showed:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/unicode-startup1.png"><img class="alignnone size-full wp-image-1554" title="unicode-startup1" src="http://mikefrobbins.com/wp-content/uploads/2010/12/unicode-startup1.png" alt="" width="575" height="385" /></a>

Attempting to disable these two entries via MSConfig generates an access denied error. The normal spyware programs that I run haven’t been able to detect these items either. The only way I’ve found to disable them is via the registry:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/unicode-startup2.png"><img class="alignnone size-full wp-image-1555" title="unicode-startup2" src="http://mikefrobbins.com/wp-content/uploads/2010/12/unicode-startup2.png" alt="" width="640" height="463" /></a>

µ