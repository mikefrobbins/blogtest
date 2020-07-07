---
ID: 13207
post_title: >
  How to install the new preview version
  of the PowerShell ISE
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/01/30/how-to-install-the-new-preview-version-of-the-powershell-ise/
published: true
post_date: 2016-01-30 07:30:10
---
The PowerShell team released a new preview version of the PowerShell ISE (Integrated Scripting Environment) this week. This is the first time a new version of the PowerShell ISE has been released separately from a new version of the WMF (Windows Management Framework). This new approach reminds me of how they shipped the help separately from the WMF beginning with PowerShell version 3.0.

<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep.png" rel="attachment wp-att-13214"><img class="alignleft size-full wp-image-13214" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep.png" alt="isep" width="420" height="234" /></a>Best of All, this new preview version of the ISE is a module which is distributed via the <a href="http://www.powershellgallery.com/" target="_blank">PowerShell Gallery</a> and installing it doesn't interfere with the current version of the ISE so there's no chance of hosing up the development of any mission critical work that you may be working on. With that said, I would <span style="text-decoration: underline;">NOT</span> recommend installing this preview version on a server because GUI's belong on PC's, not servers &lt;period&gt;.

PowerShell version 5 RTM or the WMF 5 Production Preview is required so be sure to check the PowerShell version on the machine that you plan to install the ISE preview on before getting started. If you have Windows 10, the necessary PowerShell version is already installed, otherwise you can find the production preview <a href="http://blogs.msdn.com/b/powershell/archive/2015/08/31/windows-management-framework-5-0-production-preview-is-now-available.aspx" target="_blank">here</a>. This blog article was written with and all of my testing has been performed using Windows 10 Enterprise Edition version 1511.
<pre class="lang:ps decode:true">$PSVersionTable.PSVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-3a.png" rel="attachment wp-att-13211"><img class="alignnone size-full wp-image-13211" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-3a.png" alt="isep-3a" width="859" height="131" /></a>

The <em>Install-Module</em> cmdlet which is part of the PowerShellGet module is used to install the ISE preview module from the PowerShell Gallery. The PowerShell Gallery is an untrusted repository so you'll be prompted to make sure that you want to install software on your machine from an untrusted source. Be aware that although this module is written by the PowerShell team, that most of the modules on the PowerShell Gallery are written by third party's and there are potential security issues with installing software from untrusted repositories.

The <em>Force</em> parameter could be used to suppress the untrusted repository message. There is also a <em>Scope</em> parameter if you prefer to install the module only for the current user and not in the all users path.
<pre class="lang:ps decode:true">Install-Module -Name PowerShellISE-preview</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-1a.png" rel="attachment wp-att-13208"><img class="alignnone size-full wp-image-13208" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-1a.png" alt="isep-1a" width="859" height="130" /></a>

Note: Specifying the <em>Scope</em> parameter with a value of <em>CurrentUser</em> installs the module only for the current user who is running PowerShell which may not be the same user who is logged into Windows depending on whether or not an alternate user was specified when running PowerShell elevated.

The <em>Install-ISEPreviewShortcut</em> cmdlet creates shortcuts in the start menu for the ISE preview:
<pre class="lang:ps decode:true ">Install-ISEPreviewShortcut</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-4a.png" rel="attachment wp-att-13212"><img class="alignnone size-full wp-image-13212" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-4a.png" alt="isep-4a" width="859" height="60" /></a>

Note: The shortcuts are created in the user's start menu who is running PowerShell. If an alternate user was specified when running PowerShell elevated, the shortcuts will be created in that users start menu, not the user who is currently logged into Windows. If you're running PowerShell as an alternate user and the ISE preview module was installed for all users, the solution to this is to run PowerShell non-elevated when creating the shortcuts.

To launch the ISE preview, enter the <em>isep</em> alias:
<pre class="lang:ps decode:true">isep</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-2a.png" rel="attachment wp-att-13210"><img class="alignnone size-full wp-image-13210" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-2a.png" alt="isep-2a" width="859" height="53" /></a>

One of the awesome things about shipping the ISE separately as a module is how quickly bug fixes can be released:
<pre class="lang:ps decode:true">Find-Module -Name PowerShellISE-preview -AllVersions | Select-Object -Property Version, Name, PublishedDate</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-5a.png" rel="attachment wp-att-13213"><img class="alignnone size-full wp-image-13213" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-5a.png" alt="isep-5a" width="859" height="141" /></a>

Updating the ISE preview is as easy as running <em>Update-Module:</em>
<pre class="lang:ps decode:true">Update-Module -Name PowerShellISE-preview</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-7a.png" rel="attachment wp-att-13230"><img class="alignnone size-full wp-image-13230" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-7a.png" alt="isep-7a" width="859" height="129" /></a>

I noticed that a list of the bug fixes can be found on the about page:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-6a.png" rel="attachment wp-att-13228"><img class="alignnone size-full wp-image-13228" src="http://mikefrobbins.com/wp-content/uploads/2016/01/isep-6a.png" alt="isep-6a" width="614" height="458" /></a>

For more information, see the PowerShell team blog announcement: "<em><a href="http://blogs.msdn.com/b/powershell/archive/2016/01/20/introducing-the-windows-powershell-ise-preview.aspx" target="_blank">Introducing the Windows PowerShell ISE Preview</a></em>".

µ