---
ID: 12294
post_title: 'Video from Atlanta TechStravaganza 2015: Using PowerShell Desired State Configuration in your On-Premises Datacenter'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/27/video-from-atlanta-techstravaganza-2015-using-powershell-desired-state-configuration-in-your-on-premises-datacenter/
published: true
post_date: 2015-08-27 07:30:18
---
This past Friday, I presented a session titled "<a href="http://atltechstravaganza.com/2015/sessions/powershell/using-powershell-desired-state-configuration-in-your-on-premises-datacenter/" target="_blank">Using PowerShell Desired State Configuration in your On-Premises Datacenter</a>" at Atlanta TechStravaganza 2015.

Fellow PowerShell MVP <a href="http://twitter.com/FoxDeploy" target="_blank">Stephen Owen</a> took this photo as I was preparing for my session:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/using-dsc-onprem.jpg"><img class="alignnone size-large wp-image-12297" src="http://mikefrobbins.com/wp-content/uploads/2015/08/using-dsc-onprem-1002x1024.jpg" alt="using-dsc-onprem" width="640" height="654" /></a>

Another fellow PowerShell MVP, <a href="http://twitter.com/jonwalz" target="_blank">Jonathan Walz</a> of the <a href="http://powershell.org/wp/powerscripting-podcast/" target="_blank">PowerScripting Podcast</a>, recorded all of the sessions in the PowerShell track which can be found on <a href="https://www.youtube.com/channel/UC1xcgLFT2Q9UneeQCr_4WoQ" target="_blank">their YouTube channel</a>.

Several presentations are included in each of the videos and mine is approximately the first 43 minutes of this video:

[youtube=http://youtu.be/j-A0yjBIxIw]

Here's what one of my kids had to say about the video:
<h5><span style="color: #0000ff;"><em> "Daddy, that looks like a little miniature you in a little PowerShell doll house."</em></span></h5>
I received a question from one of the attendees of my session about using the Active Directory computer account ObjectGUID for the DSC LCM ConfigurationID. My response was that it wasn't a good idea and my opinion was based on a comment that one of the PowerShell team members made at the PowerShell Summit North America earlier this year. I've since followed up on that to find out why and I was directed to this blog article: "<a href="http://blogs.msdn.com/b/powershell/archive/2014/12/31/securely-allocating-guids-in-powershell-desired-state-configuration-pull-mode.aspx" target="_blank">Securely allocating GUIDs in PowerShell Desired State Configuration Pull Mode</a>".

The presentation materials to include the slide deck and code that I used during my presentation can be downloaded <a href="http://mikefrobbins.com/downloads/AtlantaTechStravaganza2015-DSC-Session.zip">here</a> and my DSC toolkit that I reference during the presentation can be downloaded as a module from <a href="https://github.com/mikefrobbins/DSC" target="_blank">my DSC repository</a> on GitHub.

Here's the content of an email that I received from one of the attendees of my session:
<h5><span style="color: #0000ff;"><em>"Really appreciated your session and presentation on DSC.  I had looked at DSC about a year or so ago, and found the task a bit overwhelming.  However, your session today made it far more approachable and I’ll be looking at implementing it with my work environment over the next couple of months, so thank you!"</em></span></h5>
It's comments like this that keep me motivated to share my knowledge with others.

µ