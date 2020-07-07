---
ID: 9181
post_title: >
  No PowerShell, you may not install Bing
  Desktop on my Computer!
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/02/20/no-powershell-you-may-not-install-bing-desktop-on-my-computer/
published: true
post_date: 2014-02-20 07:30:19
---
I recently noticed some strange behavior from PowerShell on my computer when attempting to import all of the available modules :
<pre class="lang:ps decode:true">Get-Module -ListAvailable | Import-Module -DisableNameChecking</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-2.png"><img class="alignnone size-full wp-image-9182" alt="022014-2" src="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-2.png" width="877" height="121" /></a>
<h6><span style="color: #ff0000;"><em>PS [MikeFRobbins.com]\&gt;Import-Module -Name PSWindowsUpdate</em></span>
<span style="color: #ff0000;"><em>Confirm</em></span>
<span style="color: #ff0000;"><em>Are you sure you want to perform this action?</em></span>
<span style="color: #ff0000;"><em>Performing the operation "Bing Desktop v1.3.1[9 MB]?" on target "PC01".</em></span>
<span style="color: #ff0000;"><em>[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):</em></span></h6>
I could create some elaborate script to iterate through each module and tell me what module is trying to install Bing Desktop, or I can simply use the <em>-PassThru</em> parameter:
<pre class="lang:ps decode:true">Get-Module -ListAvailable | Import-Module -DisableNameChecking -PassThru</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-3.png"><img class="alignnone size-full wp-image-9183" alt="022014-3" src="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-3.png" width="877" height="309" /></a>

As shown in the previous image, the message about wanting to install Bing Desktop shows up just after the PSTerminalServices module and just before the PSWindowsUpdate module, both of which are custom third party PowerShell modules.

In order to determine which one is causing the issue, manually import them one at a time:
<pre class="lang:ps decode:true">Import-Module -Name PSTerminalServices
Import-Module -Name PSWindowsUpdate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-4.png"><img class="alignnone size-full wp-image-9184" alt="022014-4" src="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-4.png" width="877" height="131" /></a>

The PSWindowsUpdate module which is a custom third party module that I downloaded from the TechNet script repository is causing the issue. Answering yes to the message will result in Bing Desktop being installed on your computer without any further confirmation:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-1.png"><img class="alignnone size-full wp-image-9185" alt="022014-1" src="http://mikefrobbins.com/wp-content/uploads/2014/02/022014-1.png" width="656" height="465" /></a>

I decided to revisit this issue prior to publishing this blog. I downloaded the latest version of the PSWindowsUpdate module from the TechNet script repository and it does not appear to have this issue.

The moral of the story is that if you experience issues with a PowerShell module, especially a custom third party one, make sure you have the latest version because your issue may have already been resolved in the newest version :-).

µ