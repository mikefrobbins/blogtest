---
ID: 17160
post_title: >
  Use PowerShell to Install the Remote
  Server Administration Tools (RSAT) on
  Windows 10 version 1809
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/10/03/use-powershell-to-install-the-remote-server-administration-tools-rsat-on-windows-10-version-1809/
published: true
post_date: 2018-10-03 07:30:25
---
My computer recently updated to Windows 10 version 1809 and as with all previous major updates of Windows 10, this wipes out the Remote Server Administration Tools (RSAT).

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/win10-1809.jpg"><img class="alignnone size-full wp-image-17177" src="https://mikefrobbins.com/wp-content/uploads/2018/10/win10-1809.jpg" alt="" width="859" height="187" /></a>

However, unlike previous versions, Microsoft has now made RSAT available via Features on Demand and while you're supposed to be able to install them from the GUI, they never showed up as being an option for me.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat1a.jpg"><img class="alignnone size-full wp-image-17162" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat1a.jpg" alt="" width="859" height="348" /></a>

That's not really a problem though since they can now be installed via PowerShell. Who needs a GUI anyway?

The computer used in this blog article runs Windows 10 Enterprise Edition version 1809 with Windows PowerShell version 5.1 which is the default version of PowerShell that ships with that operating system. The execution policy has been set to Remote Signed (the default is Restricted), although it may not matter for this installation.

The commands used in this blog article are part of the <span class="ILfuVd">Deployment Image Servicing and Management</span> (DISM) PowerShell module that's installed by default on Windows 10.
<pre class="lang:ps decode:true">Get-Command -Noun WindowsCapability</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat2a.jpg"><img class="alignnone size-full wp-image-17163" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat2a.jpg" alt="" width="859" height="153" /></a>

Determine which individual tools are available.

Notice that I specified the <em>Name</em> parameter with RSAT and a wildcard at the end of it as the value instead of piping to <em>Where-Object</em>. This is called filtering left (it's more efficient than piping to <em>Where-Object</em>).
<pre class="lang:ps decode:true ">Get-WindowsCapability -Name RSAT* -Online</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat3a.jpg"><img class="alignnone size-full wp-image-17165" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat3a.jpg" alt="" width="859" height="560" /></a>

It's easier to see what's available by piping to <em>Select-Object</em> or <em>Format-Table</em> and only selecting a couple of the properties.

While it didn't matter in this scenario, I generally pipe to <em>Select-Object</em> if I have four or fewer properties and want the output in a table. Why? Because <em>Select-Object</em> returns objects that are usable by other commands in case I wanted to pipe the results to something else. Unless custom formatting has been applied, five properties would result in a list by default in which case I'd have to pipe to <em>Format-Table</em> to return the results in a table.
<pre class="lang:ps decode:true">Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property DisplayName, State</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat4a.jpg"><img class="alignnone size-full wp-image-17166" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat4a.jpg" alt="" width="859" height="371" /></a>

I want to install them all. The simplest way is to pipe the results from the <em>Get-WindowsCapability</em> command to the <em>Add-WindowsCapability</em> command. How did I know I could pipe one to the other? I read <a href="https://docs.microsoft.com/en-us/powershell/module/dism/add-windowscapability" target="_blank" rel="noopener">the help</a>. You could specify the name of individual features on demand if you wanted to be more selective on which tools to install.
<pre class="lang:ps decode:true ">Get-WindowsCapability -Name RSAT* -Online | Add-WindowsCapability -Online</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat5a.jpg"><img class="alignnone size-full wp-image-17167" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat5a.jpg" alt="" width="859" height="219" /></a>

If the <em>Add-WindowsCapacity</em> command was written using best practices, you could have specified the <em>Confirm</em> parameter and walked through each Feature on Demand selecting whether or not to install it, but unfortunately support for <em>WhatIf</em> and <em>Confirm</em> wasn't added to it. All PowerShell commands that make changes should support <em>WhatIf</em> and <em>Confirm</em> otherwise the changes could result in a Resume generating event.

Confirm that the tools were indeed installed successfully.
<pre class="lang:ps decode:true ">Get-WindowsCapability -Name RSAT* -Online | Select-Object -Property DisplayName, State</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat6a.jpg"><img class="alignnone size-full wp-image-17168" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat6a.jpg" alt="" width="859" height="371" /></a>

Once installed, they also showed up as being installed in the GUI.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat7a.jpg"><img class="alignnone size-full wp-image-17170" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat7a.jpg" alt="" width="859" height="485" /></a>

Don't forget to update the help.
<pre class="lang:ps decode:true ">Update-Help</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat8a.jpg"><img class="alignnone size-full wp-image-17171" src="https://mikefrobbins.com/wp-content/uploads/2018/10/1809rsat8a.jpg" alt="" width="859" height="125" /></a>

Notice that I didn't use aliases or positional parameters in this blog article. Anytime you're sharing or saving code, full command and parameter names should be used as it's easier to follow and more self-documenting. Think about the next guy, it could be you.

<hr />

Update - October 4th, 2018
If you happen to receive error 0x800f0954, <a href="https://www.reddit.com/r/PowerShell/comments/9l33mr/use_powershell_to_install_the_remote_server/" target="_blank" rel="noopener">see the comments in this Reddit post</a>.

<hr />

Update - November 16, 2018
Be sure to view <a href="https://www.techsnips.io/snips/using-powershell-to-install-the-remote-server-administration-tools-on-windows-10-version-1809/" target="_blank" rel="noopener">my TechSnips video</a> on the same subject. It contains some additional information.

µ