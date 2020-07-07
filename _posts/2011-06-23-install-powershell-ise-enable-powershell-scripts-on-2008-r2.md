---
ID: 2414
post_title: 'Install the PowerShell ISE &#038; Enable PowerShell Scripts'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/06/23/install-powershell-ise-enable-powershell-scripts-on-2008-r2/
published: true
post_date: 2011-06-23 07:30:26
---
PowerShell 2.0 is installed by default on Windows 7 and Windows Server 2008 R2. The PowerShell ISE (Integrated Scripting Environment) is installed by default on Windows 7, but not Windows Server 2008 R2. You can use the following information to install the ISE on your 2008 R2 server (as long as it’s running the full GUI and not the Core installation).

Launch PowerShell and execute the following:
<pre class="lang:ps decode:true">Import-Module ServerManager
Add-WindowsFeature PowerShell-ISE</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise1.png"><img class="alignnone size-full wp-image-2416" title="ise1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise1.png" width="633" height="138" /></a>

If you attempted to run this on the Core (no GUI) installation of Windows Server 2008 R2, you would receive the following error:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise2.png"><img class="alignnone size-full wp-image-2418" title="ise2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise2.png" width="640" height="210" /></a>

The PowerShell ISE is now installed and can be accessed under the "Start&gt;All Programs&gt;Accessories&gt;Windows PowerShell" Menu:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise3.png"><img class="alignnone size-full wp-image-2420" title="ise3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise3.png" width="224" height="451" /></a>

PowerShell scripts are disabled by default on both Windows 7 and Windows Server 2008 R2. Typing commands into the ISE and executing them will work, but once they are saved as a script, you will receive the following error when you attempt to execute it:
<span style="color: #ff0000;"><em>ScriptName.ps1 cannot be loaded because the execution of scripts is disabled on this system. Please see "get-help about_signing" for more details.</em></span>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise4.png"><img class="alignnone size-full wp-image-2421" title="ise4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise4.png" width="640" height="457" /></a>

Using the following command, the execution of scripts is set to "RemoteSigned" which requires that scripts downloaded from the Internet be signed by a trusted publisher:
<pre class="lang:ps decode:true">Set-ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise5.png"><img class="alignnone size-full wp-image-2422" title="ise5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise5.png" width="640" height="116" /></a>

The execution policy can be changed for a single session:
<pre class="lang:batch decode:true crayon-selected">PowerShell.exe -ExecutionPolicy RemoteSigned</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/06/ise6.png"><img class="alignnone size-full wp-image-2425" title="ise6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/06/ise6.png" width="417" height="222" /></a>

The execution policy can also be set via group policy. You can learn more about <a href="http://technet.microsoft.com/en-us/library/dd347641.aspx" target="_blank">execution policies</a> on Microsoft TechNet.

µ