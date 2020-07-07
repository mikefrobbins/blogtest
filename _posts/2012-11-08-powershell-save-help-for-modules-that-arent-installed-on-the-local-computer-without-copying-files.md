---
ID: 5775
post_title: 'PowerShell: Save-Help for Modules that Aren’t Installed on the Local Computer without Copying Files'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/08/powershell-save-help-for-modules-that-arent-installed-on-the-local-computer-without-copying-files/
published: true
post_date: 2012-11-08 07:30:09
---
In <a href="http://mikefrobbins.com/2012/11/06/powershell-save-help-for-modules-that-arent-installed-on-the-local-computer/" target="_blank">my last blog article on this subject</a>, I showed how to copy the PowerShell module manifest file from a server with the module you want to download the help for <del>to the local computer</del> to trick it into thinking the module was installed locally, but copying files can get messy and isn't really a robust way of accomplishing that task.

The first method I used did get me thinking about this process though. If all the <em>Save-Help</em> cmdlet does is to look for the Module manifest file somewhere in the PowerShell Module Path, then why not point the $env:PSModulePath variable to the location of the modules on the servers you want to save the help for instead of copying the manifest files?

On the Windows 8 computer I'm using, $env:PSModulePath looks like this by default:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help101.png"><img class="alignnone size-full wp-image-5776" title="save-help101" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help101.png" height="82" width="634" /></a>

As with my previous blog on this subject, this process is unsupported. I'm going to add the UNC path location to the modules on my domain controller, SQL server, and IIS web server to this variable. You could backup the current value of the variable by piping it to the <em>Out-File</em> cmdlet, but the changes to it will only exist for the lifetime of the current PowerShell session so saving the current value is unnecessary.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help102.png"><img class="alignnone size-full wp-image-5777" title="save-help102" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help102.png" height="160" width="638" /></a>

I ended up with a space between each path, so I'll use the replace method to remove them:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help103.png"><img class="alignnone size-full wp-image-5778" title="save-help103" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help103.png" height="119" width="636" /></a>

Nothing currently exists in the location I plan to save the help to:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help104.png"><img class="alignnone size-full wp-image-5779" title="save-help104" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help104.png" height="41" width="365" /></a>

I used the <em>Save-Help</em> cmdlet and specified the -DestinationPath parameter. I also used the -Force parameter since I'd already tested this a couple of times and if you want to download the help more than once a day, it's necessary, but since several modules are duplicated in the path now, I wouldn't recommend using the -Force parameter otherwise you'll end up downloading the same help files multiple times and just overwriting them.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help106.png"><img class="alignnone size-full wp-image-5781" title="save-help106" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help106.png" height="430" width="640" /></a>

I can see that the help was downloaded for several modules that are not installed on the local computer:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help107.png"><img class="alignnone size-full wp-image-5782" title="save-help107" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help107.png" height="442" width="640" /></a>

Here's where I verified the help files were actually saved to the specified destination. The first two in the list, ActiveDirectory and ADDSDeployment, are examples of modules that don't exist on the local computer:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help108a.png"><img class="alignnone size-full wp-image-5790" title="save-help108a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help108a.png" height="325" width="634" /></a>

<span style="text-decoration:underline;">Update 11/9/12:
</span>I've crossed out a phrase in the first paragraph because we're actually downloading the help to a network share (not to the local computer). The goal here is to download and save the PowerShell help files to a network share from a machine that has Internet access. This allows servers that do not have Internet access to update their PowerShell help by specifying the network share as the source path when running the <em>Update-Help</em> cmdlet.

The problem I'm working-around in this blog is being able to download the help files for modules that do not exist on the computer the <em>Save-Help</em> cmdlet is being run from, but do exist on servers that don't have Internet access. I would also recommend reading <a href="http://mikefrobbins.com/2012/11/06/powershell-save-help-for-modules-that-arent-installed-on-the-local-computer/" target="_blank">part 1</a> of this blog article which gives more background information on the problem.

µ