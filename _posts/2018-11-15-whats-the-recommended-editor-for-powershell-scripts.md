---
ID: 17388
post_title: 'What&#8217;s the Recommended Editor for PowerShell Scripts?'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/11/15/whats-the-recommended-editor-for-powershell-scripts/
published: true
post_date: 2018-11-15 07:30:58
---
You've probably heard, as I have, that <a href="https://code.visualstudio.com/" target="_blank" rel="noopener">Visual Studio Code (VSCode)</a> is the latest whiz-bang editor that you should be using for PowerShell (and I am for development of PowerShell code on my primary workstation). One word of caution though is to make sure to put things into perspective and not be so quick to shun people away from using the PowerShell Integrated Scripting Environment (ISE), depending on how they're using it.

I recently received a comment about my choice of editors for a particular project I was working on. This comment was from a third party and not Microsoft themselves.
<blockquote>"We do recommend using VSCode over the ISE whenever possible since it is the recommended tool by Microsoft."</blockquote>
I was wondering if I'd missed an announcement so I decided to share this comment with Microsoft and my fellow MVP's. While I didn't receive any feedback from Microsoft, I did receive numerous messages back from other MVP's and based on their comments, this message appears to be a bit strong and possibly within the context of development for PowerShell tooling. I also couldn't find anything on Microsoft's site confirming this statement.

The following is an except from "<a href="https://blogs.msdn.microsoft.com/powershell/2017/05/10/announcing-powershell-for-visual-studio-code-1-0/" target="_blank" rel="noopener">Announcing PowerShell for Visual Studio Code 1.0</a>" on the PowerShell Team blog.

<hr />

<em><strong>"What does this mean for the PowerShell ISE?</strong></em>

<em>The PowerShell ISE has been the official editor for PowerShell throughout most of the history of Windows PowerShell. Now with the advent of the cross-platform PowerShell Core, we need a new official editor that’s available across all supported OS platforms and versions. Visual Studio Code is now that editor and the majority of our effort will be focused there.</em>

<em>However, the PowerShell ISE will remain in Windows supporting Windows PowerShell with no plans to remove it. We will consider investing effort there in the future if there is a high demand for it, but for now we think that we will be able to provide the best possible experience to the PowerShell community through Visual Studio Code."</em>

<hr />

As you can see, the PowerShell ISE isn't going anywhere. While there are numerous legitimate reasons to use VSCode instead of the ISE, if a Windows admin occasionally opens a script to edit it, there's no reason they need to install VSCode. Many Windows admins would simply go back to the GUI if they were forced to install and use a product with Visual Studio in its name on day one of their scripting journey.

If you're only running scripts, you don't need the ISE or VSCode. Just run them in the PowerShell console. Nine out of ten times, when you find an intermediate level PowerShell scripter who doesn't understand scoping, it's because they never use the console.

If you're like me and spend the majority of your day knee deep in PowerShell code, then I recommend taking the time to learn how to use VSCode (See my "<a href="https://mikefrobbins.com/2017/08/24/how-to-install-visual-studio-code-and-configure-it-as-a-replacement-for-the-powershell-ise/" target="_blank" rel="noopener">How to install Visual Studio Code and configure it as a replacement for the PowerShell ISE</a>" blog article and video), but you shouldn't feel embarrassed if you're an occasional scripter who uses the ISE.

µ