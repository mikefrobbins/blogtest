---
ID: 4844
post_title: >
  No Soup, I Mean No PowerShell -Version 2
  For You Windows 8 and Windows Server
  2012
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/26/no-soup-i-mean-no-powershell-version-2-for-you-windows-8-and-windows-server-2012/
published: true
post_date: 2012-07-26 07:30:48
---
Be sure to start off by reading my previous blog article titled "<a href="http://mikefrobbins.com/2012/07/24/error-when-running-powershell-version-2-on-windows-8-and-windows-server-2012/" target="_blank">Error When Running PowerShell -Version 2 on Windows 8 and Windows Server 2012</a>" if you have not previously read it since attempting to run PowerShell in version 2 mode generates an error on a default installation of Windows 8 or Windows Server 2012.

In PowerShell version 3, you can access PowerShell version 2 by running "<em>PowerShell.exe -Version 2</em>" (or <em>"powershell -v 2"</em>):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-1.jpg"><img class="alignnone size-full wp-image-4845" title="no-psv2-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-1.jpg" width="506" height="299" /></a>

<strong>Windows 8 Release Preview</strong>

The PowerShell version 2 functionality is an optional feature that's enabled by default:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-2.jpg"><img class="alignnone size-full wp-image-4846" title="no-psv2-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-2.jpg" width="425" height="373" /></a>

PowerShell (or the GUI as shown above) can be used to display these optional features:
<pre class="lang:ps decode:true">#Display the operating system version to show this is Windows 8 Release Preview:
(Get-CimInstance -ClassName Win32_OperatingSystem).Caption

#List the features that are currently enabled:
Get-WindowsOptionalFeature -Online | where State -EQ 'enabled'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-252.jpg"><img class="alignnone size-full wp-image-5030" title="no-psv2-252" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-252.jpg" width="608" height="264" /></a>

The thing I don't like about the <em>Get-WindowsOptionalFeature</em> cmdlet that I used in the previous example is that its FeatureName parameter doesn't accept wildcards or an array of strings which makes that particular parameter almost useless:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-261.jpg"><img class="alignnone size-full wp-image-4986" title="no-psv2-261" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-261.jpg" width="640" height="262" /></a>

It's possibly to remove the PowerShell version 2 functionality by disabling those features:
<pre class="lang:ps decode:true">Disable-WindowsOptionalFeature -Online -FeatureName 'MicrosoftWindowsPowerShellV2', 'MicrosoftWindowsPowershellV2Root'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-3.jpg"><img class="alignnone size-full wp-image-4853" title="no-psv2-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-3.jpg" width="640" height="148" /></a>

At least the <em>Disable-WindowsOptionalFeature</em> cmdlet supports an array of strings so you can disable more than one feature at the same time. <em>Enable-WindowsOptionalFeature</em> also supports an array of strings.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-31.jpg"><img class="alignnone size-full wp-image-4987" title="no-psv2-31" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-31.jpg" width="581" height="164" /></a>

<strong>Windows Server 2012 Release Candidate</strong>

PowerShell version 2 doesn't show up in the GUI (if you performed the full GUI installation of the OS) as a feature that can be disabled, but it does show up in PowerShell:
<pre class="lang:ps decode:true">#Display the operating system version to show this is Windows Server 2012 Release Candidate:
(Get-CimInstance -ClassName Win32_OperatingSystem).Caption

#List the features that start with PowerShell:
Get-WindowsFeature -Name PowerShell*</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-42.jpg"><img class="alignnone size-full wp-image-5031" title="no-psv2-42" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-42.jpg" width="640" height="175" /></a>

As with Windows 8, the PowerShell version 2 functionality can be disabled in Windows Server 2012 by disabling this feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-51.jpg"><img class="alignnone size-full wp-image-4994" title="no-psv2-51" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/no-psv2-51.jpg" width="640" height="198" /></a>

My <a href="http://mikefrobbins.com/2012/07/24/error-when-running-powershell-version-2-on-windows-8-and-windows-server-2012/" target="_blank">previous blog</a> (referenced in the first paragraph of this blog) includes more information about the new <em>Install-WindowsFeature</em> and <em>Uninstall-WindowsFeature</em> cmdlets.

µ