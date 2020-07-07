---
ID: 12133
post_title: >
  Using PowerShell for Remote Server
  Administration in Windows 10 RTM without
  the RSAT tools
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2015/08/05/using-powershell-for-remote-server-administration-in-windows-10-rtm-without-the-rsat-tools/
published: true
post_date: 2015-08-05 07:30:27
---
You've updated to the RTM version of Windows 10 only to learn that the remote server administration tools aren't available as of yet:
<pre class="lang:ps decode:true">Get-ADUser -Identity mikefrobbins</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround1a.jpg"><img class="alignnone size-full wp-image-12134" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround1a.jpg" alt="win10-rsat-workaround1a" width="859" height="152" /></a>

Let's say for example the Active Directory PowerShell module is something that you use on a daily basis and it's necessary to perform your day to day responsibilities. Well, you're out of luck because the RSAT tools aren't available yet, but before you consider using RDP or the GUI to perform your duties, let's take a look at a viable solution in PowerShell.

Create a PSSession to one of your domain controllers, specifying alternate credentials if necessary:
<pre class="lang:ps decode:true">$session = New-PSSession -ComputerName vmhost -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround2a.jpg"><img class="alignnone size-full wp-image-12135" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround2a.jpg" alt="win10-rsat-workaround2a" width="859" height="106" /></a>

If the specified domain controller is running PowerShell version 2, you'll need to import the Active Directory module, but if it's running PowerShell version 3 or higher, you can skip this step:
<pre class="lang:ps decode:true">Invoke-Command -Session $session {Import-Module -Name ActiveDirectory}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround3a.jpg"><img class="alignnone size-full wp-image-12137" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround3a.jpg" alt="win10-rsat-workaround3a" width="859" height="61" /></a>

Import the PSSession and specify the Active Directory module:
<pre class="lang:ps decode:true">Import-PSSession -Session $session -Module ActiveDirectory</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround4a.jpg"><img class="alignnone size-full wp-image-12138" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround4a.jpg" alt="win10-rsat-workaround4a" width="859" height="133" /></a>

What we've done is to use implicit remoting to create shortcuts of the Active Directory cmdlets on the local computer from the domain controller that was previously specified. When the active directory cmdlets are executed, they run on the domain controller through the PSSession that was created but it will seem as if they exist locally. The problem though is this has to be recreated each time PowerShell is restarted. Or does it? Wouldn't it be awesome to be able to make these shortcuts to the Active Directory cmdlets persistent so they exist each time PowerShell is opened on your local computer?

All that's required to accomplish this is a pinch of pixie dust that can be retrieved from a Leprechaun at the end of the rainbow. Although it does seem like magic, all we really need to do to accomplish this is to simply use the <em>Export-PSSession</em> cmdlet to save the module:
<pre class="lang:ps decode:true">Export-PSSession -Session $session -Module ActiveDirectory -OutputModule "$env:ProgramFiles\WindowsPowerShell\Modules\ActiveDirectory" -FormatTypeName * -AllowClobber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround5b.jpg"><img class="alignnone size-full wp-image-12156" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround5b.jpg" alt="win10-rsat-workaround5b" width="859" height="214" /></a>

I've now closed my PowerShell console and reopened it. Now when I try to use one of the cmdlets that's part of the Active Directory module, the PSSession is automatically recreated without having to jump through the hoops of setting up implicit remoting again:
<pre class="lang:ps decode:true  ">Get-ADUser -Identity mikefrobbins</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround6a.jpg"><img class="alignnone size-full wp-image-12141" src="http://mikefrobbins.com/wp-content/uploads/2015/08/win10-rsat-workaround6a.jpg" alt="win10-rsat-workaround6a" width="859" height="262" /></a>

When the RSAT tools become available, I'll remove the local Active Directory module that was created with the <em>Export-PSSession</em> cmdlet, but until then, this works like a charm.

Note: PowerShell remoting must be enabled on the domain controller that you're attempting to use with implicit remoting as shown in this blog article, but if it's running Windows Server 2012 or higher, PowerShell remoting is already enabled by default.

µ