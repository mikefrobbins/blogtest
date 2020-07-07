---
ID: 9380
post_title: >
  How to Install the Preview Version of
  PowerShell Version 5
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/06/how-to-install-the-preview-version-of-powershell-version-5/
published: true
post_date: 2014-04-06 07:30:47
---
The <a href="http://blogs.technet.com/b/windowsserver/archive/2014/04/03/windows-management-framework-v5-preview.aspx" target="_blank">Windows Management Framework v5 Preview</a> was announced and released a few days ago (on April 3, 2014) which includes a preview version of PowerShell version 5.

Be sure to read through the System Requirements on the actual <a href="http://www.microsoft.com/en-us/download/details.aspx?id=30653" target="_blank">download page</a> before attempting to install this preview version because it's not recommended to be installed at this time on systems with certain software installed such as Exchange Server 2007.

I didn't see this stated on the download page and I think it goes without saying, but I wouldn't recommend installing this preview version in a production environment.

If you want to try out the preview version of PowerShell version 5, you'll need a machine with either Windows 8.1 Enterprise, Pro, or a machine with Windows Server 2012 R2.

All of those OS's already have PowerShell version 4 installed by default:
<pre class="lang:ps decode:true">$PSVersionTable.PSVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-16-45.png"><img class="alignnone size-full wp-image-9381" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-16-45.png" alt="2014-04-06_13-16-45" width="877" height="132" /></a>

The installation instructions also state that the .NET Framework 4.5 is required which is installed by default on the OS's that the preview is supported on.

<a href="http://www.microsoft.com/en-us/download/details.aspx?id=30653" target="_blank"><img class="alignnone size-full wp-image-9401" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_14-06-43.png" alt="2014-04-06_14-06-43" width="547" height="187" /></a>

I didn't see whether or not the full (and not the client) version was required, but the download link points you to the full version and previous versions of PowerShell required the full version of the .NET framework so I'll double check to make sure it's installed on my test system:
<pre class="lang:ps decode:true">(Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full').version</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-30-20.png"><img class="alignnone size-full wp-image-9385" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-30-20.png" alt="2014-04-06_13-30-20" width="877" height="72" /></a>

Following the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=30653" target="_blank">download link</a> for the .NET Framework (as shown in the following image) only shows down level OS's such as Windows 7 and 2008 R2 so that maybe a clue that the release version of PowerShell version 5 will be available on down level OS's, but that's only me speculating at this point and nothing official.

<a href="http://www.microsoft.com/en-us/download/details.aspx?id=42316" target="_blank"><img class="alignnone size-full wp-image-9391" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-49-24.png" alt="2014-04-06_13-49-24" width="573" height="44" /></a>

Looks like I'm all set. I've downloaded the 64 bit version of the preview since my test system is running the 64 bit version of Windows 8.1 Enterprise.

I've double-clicked on the downloaded file:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_12-53-51.png"><img class="alignnone size-full wp-image-9383" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_12-53-51.png" alt="2014-04-06_12-53-51" width="366" height="216" /></a>

The install completed without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-01-38.png"><img class="alignnone size-full wp-image-9384" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-01-38.png" alt="2014-04-06_13-01-38" width="561" height="396" /></a>

I've opened PowerShell and it now shows PowerShell version 5 as the installed version:
<pre class="lang:ps decode:true">$PSVersionTable.PSVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-04-07.png"><img class="alignnone size-full wp-image-9386" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-06_13-04-07.png" alt="2014-04-06_13-04-07" width="877" height="137" /></a>

I'll be following up with future blogs on the new features in PowerShell version 5, although as the "Additional Information" section of the WMF 5.0 download page specifies, "Features and behavior are likely to change before the final release."

µ