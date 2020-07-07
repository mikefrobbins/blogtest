---
ID: 11015
post_title: 'How to check the PowerShell version &#038; install a new version'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/01/08/how-to-check-the-powershell-version-and-install-a-new-version/
published: true
post_date: 2015-01-08 07:30:38
---
In my last blog article I demonstrated "<a href="http://mikefrobbins.com/2015/01/01/where-to-find-and-how-to-launch-powershell/" target="_blank">Where to Find &amp; How to Launch PowerShell</a>".

Today I'll continue on to the next step which is determining what version of PowerShell you have installed. PowerShell version information is contained in the <em>$PSVersionTable</em> automatic variable.

You can type out <em>$PSVersionTable</em> in the console window that you opened in the last blog article or you can type <em>$psv</em> and press tab to take advantage of what's called tabbed expansion:
<pre class="lang:ps decode:true">$PSVersionTable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2a.jpg"><img class="alignnone size-full wp-image-11016" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2a.jpg" alt="ps-day2a" width="995" height="220" /></a>

In the previous example I checked the PowerShell version on a computer running Windows 7 that has the default version of PowerShell installed that Windows 7 ships with which is PowerShell version 2.

While PowerShell version 2 is still a viable solution and your only option if you're still running Windows Server 2003 or Windows Vista, there's very few reasons not to upgrade to a newer version of PowerShell if your operating system supports it.

There are certain versions of Exchange, System Center, and various other applications or third party products that you may have installed which may not support a newer version of PowerShell or the .NET Framework version upgrade that is required so check with your application vendors before proceeding with upgrading to a newer version of PowerShell.

<span style="color: #ff0000;"><strong>Warning: </strong><em>You should always test new software (to include a new version of PowerShell) in a test environment before installing it on a production system. Do not install preview versions of PowerShell in a production environment. &lt;period&gt;</em></span>

Here's a slide from one of my presentations that shows what PowerShell versions ship with all of the currently supported Microsoft operating system versions:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2b.jpg"><img class="alignnone size-full wp-image-11018" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2b.jpg" alt="ps-day2b" width="629" height="378" /></a>

I'll download and install PowerShell version 4 on the Windows 7 computer that's being used in this blog article. PowerShell is distributed as part of the Windows Management Framework which you'll sometimes see the acronym WMF for.

Download the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=40855" target="_blank">Windows Management Framework 4.0</a>. Read through the system requirements and make sure that your system meets the requirements. Windows 7 requires that service pack 1 be installed so let's validate it's installed:
<pre class="lang:ps decode:true">Get-WmiObject -Class Win32_OperatingSystem | Format-Table Caption, ServicePackMajorVersion -AutoSize</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2c.jpg"><img class="alignnone size-full wp-image-11022" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2c.jpg" alt="ps-day2c" width="995" height="148" /></a>

The full version of the <a href="http://www.microsoft.com/en-us/download/details.aspx?id=30653" target="_blank">.NET Framework 4.5</a> is also a prerequisite. A simple PowerShell one-liner can be used to check to see whether or not it's installed:
<pre class="lang:ps decode:true">(Get-ItemProperty -Path 'HKLM:\Software\Microsoft\NET Framework Setup\NDP\v4\Full' -ErrorAction SilentlyContinue).Version -like '4.5*'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2d.jpg"><img class="alignnone size-full wp-image-11025" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2d.jpg" alt="ps-day2d" width="995" height="112" /></a>

As you can see in the previous results, the .NET Framework 4.5 is not currently installed. Keep in mind that the full version of the .NET Framework 4.5 is required and the client version is not sufficient. You'll receive the error “<a href="http://mikefrobbins.com/2012/11/03/when-trying-to-install-powershell-version-3-0-you-receive-the-message-the-update-is-not-applicable-to-your-computer/" target="_blank">The Update is Not Applicable to Your Computer</a>” if the client version is installed and not the full version when trying to install the Windows Management Framework 4.0.

I've downloaded and installed the .NET Framework 4.5 so let's rerun the last command to see if it now returns true:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2f.jpg"><img class="alignnone size-full wp-image-11028" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2f.jpg" alt="ps-day2f" width="995" height="113" /></a>

Indeed it does.

I've now installed the Windows Management Framework 4.0. A restart was required after the installation finished. Let's check the PowerShell version again:

<a href="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2g.jpg"><img class="alignnone size-full wp-image-11029" src="http://mikefrobbins.com/wp-content/uploads/2015/01/ps-day2g.jpg" alt="ps-day2g" width="995" height="208" /></a>

PowerShell version 4 has been successfully installed on the Windows 7 computer used in the demonstration in this blog article. Yes, it's that easy so why are you still using an older version of PowerShell?

µ