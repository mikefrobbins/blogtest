---
ID: 5739
post_title: 'PowerShell: Save-Help for Modules that Aren&#8217;t Installed on the Local Computer'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/11/06/powershell-save-help-for-modules-that-arent-installed-on-the-local-computer/
published: true
post_date: 2012-11-06 07:30:37
---
<a href="http://technet.microsoft.com/en-us/library/hh849720.aspx" target="_blank"><em>Update-Help</em></a> and <a href="http://technet.microsoft.com/en-us/library/hh849724.aspx" target="_blank"><em>Save-Help</em></a> are new PowerShell version 3 cmdlets which allow you to initially download PowerShell help and keep the local PowerShell help files updated that are installed on your computer and available via the <em>Get-Help</em> cmdlet.

In PowerShell version 3, <em>Save-Help</em> allows the updated help files to be saved to a location where a machine that doesn't have Internet access can update it's help from. The problem is that in order to save help for a module, that module has to exist on the local computer that's performing the actual download otherwise an error will be generated:

<span style="color:#ff0000;">Save-Help : No Windows PowerShell modules were found that match the following pattern: </span>
<span style="color:#ff0000;">ServerCore. Verify the pattern and then try the command again.</span>
<span style="color:#ff0000;">At line:1 char:1</span>
<span style="color:#ff0000;">+ Save-Help -Module ServerCore -DestinationPath \dc01PSHelp -Force</span>
<span style="color:#ff0000;">+~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span>
<span style="color:#ff0000;"> + CategoryInfo : InvalidArgument: (ServerCore:String) [Save-Help], Except</span><span style="color:#ff0000;">ion</span>
<span style="color:#ff0000;"> + FullyQualifiedErrorId : ModuleNotFound,Microsoft.PowerShell.Commands.SaveHelpCom</span><span style="color:#ff0000;">mand</span>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help1.png"><img class="alignnone size-full wp-image-5741" title="save-help1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help1.png" height="133" width="640" /></a>

Now this is definitely unsupported, but what I've found is you can copy the module manifest file (.psd1 file) from a machine that has the module installed to the computer you're going to run the <em>Save-Help</em> cmdlet on to trick it into thinking the module is installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help2b.png"><img class="alignnone size-full wp-image-5764" title="save-help2b" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help2b.png" height="155" width="640" /></a>

So all I've done is created a sub folder in my modules directory and place the manifest file in it for the module that's not installed but that I want to download the help for:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help3.png"><img class="alignnone size-full wp-image-5743" title="save-help3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help3.png" height="247" width="441" /></a>

Once that's complete, saving the help for that particular module works without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help4.png"><img class="alignnone size-full wp-image-5744" title="save-help4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help4.png" height="41" width="640" /></a>

This flashes by fairly quickly when the help is being downloaded:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help5.png"><img class="alignnone size-full wp-image-5745" title="save-help5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help5.png" height="67" width="477" /></a>

Now the help for that module is located on the network share that it was directed to using the <em>Save-Update</em> cmdlet with the -DestinationPath parameter:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help6.png"><img class="alignnone size-full wp-image-5746" title="save-help6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help6.png" height="164" width="625" /></a>

On a server without an Internet connection, when trying to update the help, you'll receive the following error:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help7.png"><img class="alignnone size-full wp-image-5747" title="save-help7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help7.png" height="168" width="640" /></a>

When specifying the location using the -SourcePath parameter with <em>Update-Help</em>, the help on this server without an Internet connection completes without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help8.png"><img class="alignnone size-full wp-image-5748" title="save-help8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help8.png" height="100" width="640" /></a>

Now the cool thing about remoting being enabled by default on Windows Server 2012 is you can update the help remotely using the <em>Invoke-Command</em> cmdlet:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help9.png"><img class="alignnone size-full wp-image-5749" title="save-help9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/11/save-help9.png" height="96" width="640" /></a>

I have a couple of additional ideas about how to save the help for modules that aren't installed on the local computer so check back on Thursday for <a href="http://mikefrobbins.com/2012/11/08/powershell-save-help-for-modules-that-arent-installed-on-the-local-computer-without-copying-files/" target="_blank">part 2 of this blog article</a>.

µ