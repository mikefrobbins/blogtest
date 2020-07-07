---
ID: 13848
post_title: 'Video: Creating a Custom PowerShell Toolkit to Demystify DSC'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/04/16/video-creating-a-custom-powershell-toolkit-to-demystify-dsc/
published: true
post_date: 2016-04-16 07:30:08
---
Last week, on Monday (April 4th, 2016), I presented a session at the <a href="http://powershellsummit.org" target="_blank">PowerShell and DevOps Global Summit 2016</a> on "<em>Creating a Custom PowerShell Toolkit to Demystify the Intricacies of Desired State Configuration</em>". The <a href="https://www.youtube.com/watch?v=fOov9gkqFHs" target="_blank">video</a> from that presentation is now available:

https://youtu.be/fOov9gkqFHs

Here’s the abstract or synopsis for this presentation: "<em>DSC (Desired State Configuration) can be very complicated when working in an environment where nodes are set to retrieve their configuration from a pull server. Modifying a node’s configuration, creating the necessary checksums for both the MOF configuration files and DSC resources that you plan to deploy with a pull server along with keeping track of which GUID belongs to what server can be an overwhelming task and it’s something that may not require routine modification once your environment is stable. Each time changes are needed, you could reinvent the wheel by figuring it out all over again, create a runbook that will almost certainly be out of date each time you need it, or create a custom PowerShell toolkit to automate these tasks. During this session, PowerShell MVP Mike F Robbins will walk you through his DSC toolkit which was created while configuring both physical and virtual production systems with DSC in multiple on-premises datacenters during a recent hardware and software refresh cycle. All code shown during this presentation will be made available on GitHub.</em>"

During the lightning demos on Tuesday afternoon at the PowerShell and DevOps Global Summit, one of the PowerShell team members demonstrated some tools that were very similar to mine. I've asked where they can be downloaded from but haven't received a response yet. Once I find out, I'll update this blog post with the URL.

The slide deck and code sample shown in this presentation can be downloaded from <a href="https://github.com/mikefrobbins/Presentations" target="_blank">my presentations repository on GitHub</a>. All of the DSC related functions that are referenced during the demo can be downloaded as part of <a href="https://github.com/mikefrobbins/DSC" target="_blank">my DSC repository on GitHub</a>.

µ