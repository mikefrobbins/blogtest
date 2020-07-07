---
ID: 9996
post_title: 'PowerShell Version 5 New Feature: NoWait Parameter added to Stop-Service'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/10/powershell-version-5-new-feature-nowait-parameter-added-to-stop-service/
published: true
post_date: 2014-06-10 07:30:52
---
I presented a session on "<a href="http://www.atltechstravaganza.com/presentation/whats-new-in-powershell-version-5-preview/" target="_blank">What's New in PowerShell Version 5?</a>" at TechStravaganza in Atlanta this past Friday and there was a lot of information I wasn't able to get to because of the massive amount of information that I've compiled about the updates to PowerShell version 5 that are in the May 2014 preview version so I thought I would blog about them.

He's a picture of me presenting at TechStravaganza in Atlanta this past Friday. The event was held at the Georgia Tech Research Institute Conference Center:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/mikefrobbins-speaking-at-georgia-tech.jpg"><img class="alignnone size-medium wp-image-9997" src="http://mikefrobbins.com/wp-content/uploads/2014/06/mikefrobbins-speaking-at-georgia-tech-300x225.jpg" alt="mikefrobbins-speaking-at-georgia-tech" width="300" height="225" /></a>

They must have known I was going to speak on PowerShell as the wording at the bottom of the plaque on the podium says "Problem Solved" as shown in the previous image (<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/mikefrobbins-speaking-at-georgia-tech.jpg" target="_blank">click on the image for a larger view</a>).

One of the new features is that <em>Stop-Service</em> now has a <em>NoWait</em> parameter. When you stop a service that doesn't immediately stop, by default a warning is received stating that in this example: <span style="color: #0000ff;"><em>"WARNING: Waiting for service 'IIS Admin Service (IISAdmin)' to stop..."</em></span>:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName web01 {
    Stop-Service -Name IISAdmin -PassThru
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait1.jpg"><img class="alignnone size-full wp-image-9998" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait1.jpg" alt="ps5-nowait1" width="877" height="192" /></a>

With this new <em>NoWait</em> parameter, your prompt is returned back to you immediately:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName web01 {
    Stop-Service -Name IISAdmin -NoWait -PassThru
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait2.jpg"><img class="alignnone size-full wp-image-9999" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait2.jpg" alt="ps5-nowait2" width="877" height="97" /></a>

Notice that in the previous example, specifying the <em>PassThru</em> parameter didn't cause any results to be returned when used with the <em>NoWait</em> parameter. This leads me to believe that these two parameters will end up being in mutually exclusive parameter sets in the final release version.

To give you a better idea of what's going on under the covers, I'll stop the service specifying the <em>NoWait</em> parameter and then immediately get the status of the service with the <em>Get-Service</em> cmdlet. I typically wouldn't use the <em>Format-Table</em> cmdlet in this scenario, but without the <em>AutoSize</em> parameter the value of the Status property is truncated:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName web01 {
    Stop-Service -Name IISAdmin -NoWait
    Get-Service -Name IISAdmin | Format-Table -AutoSize
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait3.jpg"><img class="alignnone size-full wp-image-10000" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-nowait3.jpg" alt="ps5-nowait3" width="877" height="180" /></a>

This blog article has been written based on the preview version of PowerShell version 5 from <a href="http://blogs.msdn.com/b/powershell/archive/2014/05/14/windows-management-framework-5-0-preview-may-2014-is-now-available.aspx" target="_blank">the May 2014 release of the Windows Management Framework 5.0 preview</a> so what you've seen here is subject to change in the final release version.

This blog article is one of many that I've written and will be writing about the new features in PowerShell version 5. You can find my other blog articles on PowerShell version 5 <a href="http://mikefrobbins.com/tag/powershell-version-5/" target="_blank">here</a>.

µ