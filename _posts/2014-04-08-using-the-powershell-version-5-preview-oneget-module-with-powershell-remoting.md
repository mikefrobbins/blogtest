---
ID: 9457
post_title: >
  Using the PowerShell version 5 Preview
  OneGet Module with PowerShell Remoting
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/08/using-the-powershell-version-5-preview-oneget-module-with-powershell-remoting/
published: true
post_date: 2014-04-08 07:30:36
---
I'm picking up where I left off in <a href="http://mikefrobbins.com/2014/04/07/installing-software-with-the-oneget-module-in-powershell-version-5/" target="_blank">my last blog article</a>. Adobe Reader, ImgBurn, and WinRAR have been installed on a Windows 8.1 Enterprise edition computer named PC03. All of the examples shown in this blog article are being run from the PC03 computer.

I'll use a little trick I showed you a few blog articles ago and store the names of the installed packages in a variable and return them at the same time, all in a one-liner:
<pre class="lang:ps decode:true">($software = Get-Package | Select-Object -ExpandProperty Name)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-58-22.png"><img class="alignnone size-full wp-image-9460" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-58-22.png" alt="2014-04-07_20-58-22" width="878" height="96" /></a>

I'll create a PSSession to the computers named PC04, PC05, and PC06, store them in a variable named $Session (technically, it's named Session) and return a list of the sessions all in a one-liner with the same trick I used in the previous command:
<pre class="lang:ps decode:true">($Session = New-PSSession -ComputerName pc04, pc05, pc06)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-43-41.png"><img class="alignnone size-full wp-image-9461" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-43-41.png" alt="2014-04-07_20-43-41" width="877" height="156" /></a>

PC04, PC05, and PC06 all run Windows 8.1 Enterprise edition, have the preview version of PowerShell version 5 installed, their script execution policy is set to RemoteSigned, and PowerShell remoting has been enabled on them. One of the cmdlets from the OneGet module has also been previous run on them to trigger installing the NuGet Package Manager, although no packages have previously been installed on them.

Note: The one package source (Chocolatey) that's included by default with the preview version of PowerShell verison 5 isn't trusted:
<pre class="lang:ps decode:true">Get-PackageSource</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_22-01-21.png"><img class="alignnone size-full wp-image-9462" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_22-01-21.png" alt="2014-04-07_22-01-21" width="877" height="133" /></a>

This means that by default you'll be prompted as shown in the following example when installing software from that package source:
<pre class="lang:ps decode:true">Find-Package -Name AdobeReader, ImgBurn, WinRAR |
Install-Package</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-37-25.png"><img class="alignnone size-full wp-image-9463" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_20-37-25.png" alt="2014-04-07_20-37-25" width="877" height="227" /></a>

Since I'll be installing the software on the three remote computers using PowerShell remoting, I'll be unable to answer those interactive questions. There are two options to prevent being prompted, remove the Chocolatey package source and re-add it as a trusted package source, or simply use the -Force parameter with the <em>Install-Package</em> cmdlet.

I'll use the -Force parameter since it's the easier solution to this problem and it doesn't permanently make changes to the package source settings:
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {Install-Package -Name $Using:software -Force}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_21-01-43.png"><img class="alignnone size-full wp-image-9464" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_21-01-43.png" alt="2014-04-07_21-01-43" width="877" height="141" /></a>

In the previous command, I've taken advantage of the Using scope modifier that was introduced in PowerShell version 3. For more information about the Using scope modifier see the <a href="http://technet.microsoft.com/en-us/library/hh847849.aspx" target="_blank">about_Scopes</a> and <a href="http://technet.microsoft.com/en-us/library/jj149005.aspx" target="_blank">about_Remote_Variables</a> help topics in PowerShell.

Success! All three of the computers (PC04, PC05, and PC06) now have the same packages installed that are installed on PC03 (Adobe Reader, ImgBurn, and WinRAR):

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_21-04-02.png"><img class="alignnone size-full wp-image-9465" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-07_21-04-02.png" alt="2014-04-07_21-04-02" width="877" height="229" /></a>

I'll be speaking for the <a href="http://mspsug.com/2014/04/01/mike-f-robbins-presenting-managing-active-directory-with-powershell-for-mspsug-on-april-8th-at-830pm-cdt/" target="_blank">Mississippi PowerShell User Group</a> tonight so sign up and join our virtual meeting (Microsoft Lync) which will be held at 8:30pm Central Time (on April 8th). I'm planning to talk about and demo what I've discovered in the PowerShell version 5 preview so far (primarily the cmdlets in the new OneGet module).

µ