---
ID: 4013
post_title: >
  Is PowerShell 2.0 Installed by Default
  on Windows Server 2008 R2?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/03/is-powershell-2-0-installed-by-default-on-windows-server-2008-r2/
published: true
post_date: 2012-05-03 07:30:21
---
As with most things in IT, the answer is that it depends. It depends on whether or not the server was installed with the Full Installation or with the Server Core Installation. It also depends on what your definition of installed is.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core01.png"><img class="alignnone size-full wp-image-4066" title="ps2-2008r2core01" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core01.png" width="640" height="480" /></a>

If the server was installed with the Full Installation (GUI) then PowerShell is installed (enabled) by default, but if it was installed using the Server Core Installation (no GUI) then PowerShell is not installed (not enabled) by default.

If you attempt to run powershell.exe on a fresh installation of  2008 R2 Server Core, it has no idea what you're talking about since that feature isn't enabled although the binaries are sitting on the hard drive and they're ready for you to enable that particular feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core1.png"><img class="alignnone size-full wp-image-4014" title="ps2-2008r2core1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core1.png" width="580" height="162" /></a>

You could use sconfig to enable the PowerShell feature on Server Core:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core2.png"><img class="alignnone size-full wp-image-4016" title="ps2-2008r2core2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core2.png" width="474" height="329" /></a>

Script execution is automatically set to "Remote Signed" as shown in the screenshot below if you enable PowerShell using this method:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core5.png"><img class="alignnone size-full wp-image-4074" title="ps2-2008r2core5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core5.png" width="640" height="317" /></a>

Sconfig actually uses the Deployment Image Service and Management Tool (DISM) behind the scenes to enable PowerShell. That's what's going on in this minimized window:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core6.png"><img class="alignnone size-full wp-image-4075" title="ps2-2008r2core6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core6.png" width="203" height="73" /></a>

Then you're prompted to restart:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core7.png"><img class="alignnone size-full wp-image-4076" title="ps2-2008r2core7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core7.png" width="399" height="197" /></a>

You could also use the Deployment Image Service and Management Tool (DISM) directly to enable the PowerShell feature on Server Core. You'll have to specify the .NET Framework 2.0 and PowerShell if you're using this method. Only specifying PowerShell will cause the installation to fail since the .NET Framework 2.0 is a prerequisite:
<pre class="lang:batch decode:true">Dism /online /enable-feature /featurename:NetFx2-ServerCore /featurename:MicrosoftWindowsPowerShell</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core3.png"><img class="alignnone size-full wp-image-4021" title="ps2-2008r2core3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core3.png" width="640" height="177" /></a>

It is technically possible to run PowerShell at this point without restarting the server, but your path environment variable hasn't been refreshed so a restart is recommended otherwise you'll have to specify the full path to the executable or manually change into the PowerShell directory to be able to run it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core4.png"><img class="alignnone size-full wp-image-4022" title="ps2-2008r2core4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core4.png" width="528" height="134" /></a>

If you go the DISM route, you'll need to manually change the script execution policy (if you want to be able to run scripts):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core8.png"><img class="alignnone size-full wp-image-4080" title="ps2-2008r2core8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core8.png" width="397" height="100" /></a>

IT Pro's should start familiarizing themselves with the Server Core Installation (no GUI) since it's the default on Windows Server 8 (aka Windows Server 2012). If you run through the installation of it without changing any of the defaults, you'll end up with server core:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core0.png"><img class="alignnone size-full wp-image-4026" title="ps2-2008r2core0" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps2-2008r2core0.png" width="637" height="477" /></a>

Regardless of which installation option you chose on Windows Server 8 (Core or GUI), PowerShell is installed by default.

µ