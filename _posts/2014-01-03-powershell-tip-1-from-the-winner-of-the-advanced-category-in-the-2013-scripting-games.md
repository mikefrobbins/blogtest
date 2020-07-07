---
ID: 8383
post_title: 'PowerShell Tip #1 from the Winner of the Advanced Category in the 2013 Scripting Games'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/01/03/powershell-tip-1-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/
published: true
post_date: 2014-01-03 07:30:42
---
In case you haven't heard, the 2014 Winter Scripting Games are just now getting started. Regardless of your skill level with PowerShell, it couldn't be a better time to participate since this is the first time in the history of the scripting games that you'll be able to work as part of a team and receive proactive feedback (before your code is judged) from a team of expert coaches who use PowerShell in the real world on a daily basis. Ultimately, the scripting games make learning PowerShell more interesting and challenging while giving you the opportunity to network with other enthusiasts in the industry.

Now it's time to talk about a PowerShell tip that I wanted to share.

<strong>Tip #1 - Read the Help!</strong>

While this may not be the most popular tip, believe it or not, it's one of the most important and it's something that's so simple it's often times overlooked. In my opinion, you'll never truly be effective with PowerShell and be able to figure things out for yourself until you learn to read the help.

I have an entire recorded presentation dedicated to the three core cmdlets that is titled "<a href="http://mikefrobbins.com/2013/03/21/florida-powershell-user-group-march-meeting-video-and-presentation-materials/" target="_blank">PowerShell Fundamentals for Beginners</a>" if you would like more information about them:
<pre class="lang:ps decode:true">Get-Help
Get-Command
Get-Member</pre>
Don't assume that you know what a parameter does based on its name. The <em>Invoke-Command</em> cmdlet has a <em>SessionName</em> parameter that was added in PowerShell version 3.

I'll create a PSSession to a computer named DC01 and I'll name the session "dcSession":
<pre class="lang:ps decode:true">New-PSSession -ComputerName dc01 -Name dcSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1a.png"><img class="alignnone size-full wp-image-8384" alt="sg-tip1a" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1a.png" width="612" height="136" /></a>

Without reading the help, it's plausible to think that you could use the PowerShell remoting <em>Invoke-Command</em> cmdlet along with its <em>SessionName</em> parameter specifying the name of the session that you just created as its value:
<pre class="lang:ps decode:true">Invoke-Command -SessionName dcSession {Get-Process}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1b.png"><img class="alignnone size-full wp-image-8385" alt="sg-tip1b" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1b.png" width="740" height="146" /></a>

Unfortunately, that wouldn't be correct. Why? Because you didn't read the help to find out what that particular parameter actually did before attempting to use it. If you had read the help first, you would have discovered that the <em>SessionName</em> parameter is only valid with the <em>InDisconnectedSession</em> parameter:
<pre class="lang:ps decode:true">Help Invoke-Command -Parameter SessionName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1c.png"><img class="alignnone size-full wp-image-8386" alt="sg-tip1c" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1c.png" width="877" height="264" /></a>

I'll now remove the PSSession that I previously created since it's no longer needed:
<pre class="lang:ps decode:true">Get-PSSession -Name dcSession | Remove-PSSession</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1d.png"><img class="alignnone size-full wp-image-8387" alt="sg-tip1d" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1d.png" width="573" height="65" /></a>

You can also teach yourself about cmdlets that you didn't know existed or more about the ones you did by reading the help. One of the tips I wrote about in a <a href="http://www.powershellmagazine.com/2012/06/27/mike-f-robbinss-favorite-powershell-tips-tricks/" target="_blank">guest article that I wrote for PowerShell Magazine</a> was how to "learn a PowerShell cmdlet a day":
<pre class="lang:ps decode:true">Get-Command | Get-Random | Get-Help -ShowWindow</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1e.png"><img class="alignnone size-full wp-image-8392" alt="sg-tip1e" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1e.png" width="632" height="365" /></a>

In addition to cmdlet help, PowerShell also has "about" help topics which contain information about various aspects of PowerShell such as concepts, keywords, scripting constructs, conditional logic, etc:
<pre class="lang:ps decode:true">help about_*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1f.png"><img class="alignnone size-full wp-image-8403" alt="sg-tip1f" src="http://mikefrobbins.com/wp-content/uploads/2013/11/sg-tip1f.png" width="877" height="859" /></a>

One of my personal favorite products for reading PowerShell help offline is <a href="http://www.sapien.com/software/ipowershell" target="_blank">iPowerShell Pro</a> which is an app for iPad and iPhone by <a href="http://www.sapien.com/" target="_blank">SAPIEN Technologies</a>.

Be sure to read the other <a href="http://powershell.org/wp/category/announcements/scripting-games/" target="_blank">articles about the scripting games on PowerShell.org</a> as they contain tips that will make your scripting games experience more enjoyable and then head on over to <a href="http://scriptinggames.org/" target="_blank">ScriptingGames.org</a> to sign up so you can participate!

Read <a href="http://powershell.org/wp/2014/01/09/powershell-tip-2-from-the-winner-of-the-advanced-category-in-the-2013-scripting-games/" target="_blank">the next article (tip #2)</a> in this series of blog articles.

µ