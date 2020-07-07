---
ID: 7556
post_title: >
  How to Create PowerShell Script Modules
  and Module Manifests
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/07/04/how-to-create-powershell-script-modules-and-module-manifests/
published: true
post_date: 2013-07-04 07:30:11
---
My entry for the Scripting Games advanced event 4 contained four separate functions:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules1.png"><img class="alignnone size-full wp-image-7557" alt="creating-modules1" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules1.png" width="498" height="211" /></a>

I want to create a module that contains these functions. There are several different types of modules, but what I'll be creating is a "Script Module". Modules sound like something really complicated, but script modules are actually simple.

Currently, I have the functions saved as a ps1 file which I dot-source to load the functions into memory, but I want to share this tool with others so it makes more sense to create a module out of it.

I'm simply going to save the existing ps1 file as a psm1 file in a folder with the same name in my modules folder as shown in the following image:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules2d.png"><img class="alignnone size-large wp-image-7564" alt="creating-modules2d" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules2d.png?w=640" width="640" height="242" /></a>

It's very important to have the folder and base name for the file the same. I've given it a generic name because I may want to add more functions to a new version of this same module in the future. I called it MrAuditTool (Mr for Mike Robbins and then Audit Tool).

At this point, it's a script module. We can see what commands it contains:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules3a.png"><img class="alignnone size-large wp-image-7565" alt="creating-modules3a" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules3a.png?w=640" width="640" height="189" /></a>

The following image contains two important things to be aware of, first shown with the purple rectangle around it, if you've imported the module you're working with or it's been imported automatically in PowerShell version 3 and changes are made to it, you'll either have to remove and re-import it or you can simply add the <em>-Force</em> parameter to the <em>Import-Module</em> command.

The second item I want you to be aware of is if you don't use standard verbs for your function names, you'll receive the warning message shown in the following image. I've placed a red rectangle around the warning and the function that doesn't use a standard verb (click on the image for a larger version which is easier to read):

<a href="http://mikefrobbins.com/wp-content/uploads/2013/07/creating-modules8.png"><img class="alignnone size-large wp-image-7662" alt="creating-modules8" src="http://mikefrobbins.com/wp-content/uploads/2013/07/creating-modules8.png?w=640" width="640" height="272" /></a>

View the output of <em>Get-Verb</em> to see a list of common verbs. There is also an "<a href="http://msdn.microsoft.com/en-us/library/windows/desktop/ms714428(v=vs.85).aspx" target="_blank">Approved Verbs for Windows PowerShell Commands</a>" article on MSDN.

Based on one of the Scripting Guy, Ed Wilson's sessions that I sat through at the PowerShell Summit a few months ago, it's best practice to create a module manifest file. I also read the following on page 315 of Lee Holmes's new <a href="http://shop.oreilly.com/product/0636920024132.do" target="_blank">PowerShell Cookbook, 3rd Edition</a> book: "<em>In addition to creating the .psm1 file that contains your module's commands, you should also create a module manifest to describe its contents and system requirements</em>".

The <em>New-ModuleManifest</em> cmdlet is used to create a module manifest. As shown below, in PowerShell version 3, the only field that you're required to provide a value for is the path:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules4a.png"><img class="alignnone size-full wp-image-7566" alt="creating-modules4a" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules4a.png" width="467" height="123" /></a>

Although, if the RootModule is not set in the Module Manifest file, it won't work and no commands at all will be exported. If you run into this problem, make sure this option is set in the psd1 module manifest file:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules7.png"><img alt="creating-modules7" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules7.png" width="529" height="38" /></a>

You could open the file with the ISE at this point and edit it manually, but I'm going to delete this manifest file and create a new one with the options I want instead:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules5a.png"><img class="alignnone size-large wp-image-7569" alt="creating-modules5a" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules5a.png?w=640" width="640" height="80" /></a>

What's really cool about the manifest file I just created is now it only exports the one <em>New-UserAuditReport</em> function since all of the others are helper functions:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules6.png"><img class="alignnone size-large wp-image-7570" alt="creating-modules6" src="http://mikefrobbins.com/wp-content/uploads/2013/06/creating-modules6.png?w=640" width="640" height="147" /></a>

There are other ways to export only specific commands in the psm1 module file instead of using this method in the psd1 module manifest file. If you're interested, read the help topic on TechNet for <em><a href="http://technet.microsoft.com/en-us/library/hh849736.aspx" target="_blank">Export-ModuleMember</a>.</em>

For more information about modules run <em>"help <a href="http://technet.microsoft.com/en-us/library/hh847804.aspx" target="_blank">about_Modules</a>"</em> from within PowerShell or read the help topic on TechNet.

<span style="line-height:1.5;">µ</span>