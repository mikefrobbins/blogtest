---
ID: 5632
post_title: >
  Piping PowerShell Output to the
  Clipboard
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/01/piping-powershell-output-to-the-clipboard/
published: true
post_date: 2012-11-01 07:30:05
---
It's definitely beneficial to attend technology events so you can talk to other IT Pro's. This past weekend I presented a session at <a href="http://powershellsaturday.com/003/" target="_blank">PowerShell Saturday in Atlanta</a> and while at the speaker dinner I made a comment about a new version of the <a href="http://pscx.codeplex.com/" target="_blank">PowerShell Community Extensions</a> being released.

The only cmdlet that's part of that module that I use all of the time is <em>Out-Clipboard</em> which allows you to paste the output of a PowerShell command into notepad, Word, an email, etc:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/out-clipboard1.png"><img class="alignnone size-full wp-image-5633" title="out-clipboard1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/out-clipboard1.png" height="229" width="640" /></a>

<a href="http://twitter.com/bwilhite1979" target="_blank">Brian Wilhite</a>, another PowerShell enthusiast and speaker gave me a great tip: "You don't need the <em>Out-Clipboard</em> cmdlet to pipe to the clipboard, just pipe your output to clip.exe":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/out-clipboard2.png"><img class="alignnone size-full wp-image-5634" title="out-clipboard2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/out-clipboard2.png" height="248" width="620" /></a>

I even demonstrated this tip during my session. Brian, thanks again for that awesome tip.

µ