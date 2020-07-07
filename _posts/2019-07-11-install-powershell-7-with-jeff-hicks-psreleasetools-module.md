---
ID: 17941
post_title: 'Install PowerShell 7 with Jeff Hicks&#8217; PSReleaseTools Module'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/07/11/install-powershell-7-with-jeff-hicks-psreleasetools-module/
published: true
post_date: 2019-07-11 07:30:41
---
PowerShell version 7 is currently in preview and while it can be installed on Windows, Linux, and macOS, the examples shown in this blog article focus on installing it on a Windows based system, specifically Windows 10 using Windows PowerShell version 5 or higher which ships by default with Windows 10. Your mileage may vary with other operating systems, other versions of Windows, and/or other versions of Windows PowerShell.

The easiest way that I've found to install the preview of PowerShell version 7 is to first install <a href="https://twitter.com/JeffHicks" target="_blank" rel="noopener noreferrer">Jeff Hicks</a>' <a href="https://www.powershellgallery.com/packages/PSReleaseTools/" target="_blank" rel="noopener noreferrer">PSReleaseTools PowerShell module from the PowerShell Gallery</a> using Windows PowerShell.
<pre class="lang:ps decode:true">Install-Module -Name PSReleaseTools -Force</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools1a.jpg"><img class="alignnone size-full wp-image-17942" src="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools1a.jpg" alt="" width="859" height="62" /></a>

The first portion of the command needs to narrow the results down to a single item. If the <em>Preview</em> parameter is omitted, this command installs the latest version of PowerShell Core version 6 instead of 7.
<pre class="lang:ps decode:true">Get-PSReleaseAsset -Family Windows -Only64Bit -Format msi -Preview |
Save-PSReleaseAsset -Path $env:USERPROFILE\Downloads -Passthru |
Invoke-Item</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools2a.jpg"><img class="alignnone size-full wp-image-17943" src="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools2a.jpg" alt="" width="859" height="139" /></a>

The remainder of the installation is a clicker-size.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools3a.jpg"><img class="alignnone size-full wp-image-17944" src="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools3a.jpg" alt="" width="859" height="478" /></a>

Want something even simpler and want to avoid the GUI clicker-size? Silently download and install the preview of PowerShell version 7 using the following command that's also part of the PSReleaseTools module.
<pre class="lang:ps decode:true">Install-PSPreview -Mode Quiet</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools4a.jpg"><img class="alignnone size-full wp-image-17953" src="https://mikefrobbins.com/wp-content/uploads/2019/07/pwsh7-psreleasetools4a.jpg" alt="" width="859" height="130" /></a>

Jeff Hicks' <a href="https://github.com/jdhitsolutions/PSReleaseTools" target="_blank" rel="noopener noreferrer">PSReleaseTools module can also be found on GitHub</a>. That's where I'd recommend reporting any problems you run into with it and I'm also sure that Jeff would welcome pull requests with bug fixes and/or feature enhancements.

Thoughts, questions, comments? Please post them as a comment to this blog article.

µ