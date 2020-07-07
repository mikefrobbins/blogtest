---
ID: 4358
post_title: Welcome to PowerShell Hell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/06/21/welcome-to-powershell-hell/
published: true
post_date: 2012-06-21 07:30:30
---
I finally figured out why the error messages in PowerShell are in bright red. It's because it's the color of flames and/or red hot coals and it means you may be in PowerShell Hell. That's what recently happened when I updated the Antivirus on my PC from Eset NOD32 version 4 to version 5. A few days after updating, I was in PowerShell Hell as shown below:

When trying to run <em>Get-ChildItem</em> against WSMan:localhost, I received the following:

<em><span style="color:#ff0000;">Get-ChildItem : WS-Management cannot process the request. The operation failed because of an </span></em><em><span style="color:#ff0000;">HTTP error. The HTTP error (12152) is: The server returned an invalid or unrecognized response . </span></em>
<em><span style="color:#ff0000;">At line:1 char:1</span></em>
<em><span style="color:#ff0000;">+ dir WSMan:localhost</span></em>
<em><span style="color:#ff0000;">+ ~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color:#ff0000;"> + CategoryInfo : NotSpecified: (:) [Get-ChildItem], InvalidOperationException</span></em>
<em><span style="color:#ff0000;"> + FullyQualifiedErrorId : System.InvalidOperationException,Microsoft.PowerShell.Commands.Get </span></em>
<em><span style="color:#ff0000;"> ChildItemCommand</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-1a.png"><img class="alignnone size-full wp-image-4376" title="ps-nod32-1a" src="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-1a.png" alt="" width="640" height="134" /></a>

When trying to use <em>Invoke-Command</em> (PowerShell Remoting), I received the following error which made me think the issue was on the destination end and not the source. I tried the same command from another machine and it worked without issue so that eliminated the computer I was trying to run PowerShell remoting commands against as the problem.

<em><span style="color:#ff0000;">[remote-pc] Connecting to remote server remote-pc failed with the following error message : The WinRM </span></em><em><span style="color:#ff0000;">client cannot process the request. The encrypted message body has an invalid format and cannot </span></em><em><span style="color:#ff0000;">be decrypted. Ensure that the service is encrypting the message body according to the </span></em><em><span style="color:#ff0000;">specifications. For more information, see the about_Remote_Troubleshooting Help topic.</span></em>
<em><span style="color:#ff0000;"> + CategoryInfo : OpenError: (:) [], PSRemotingTransportException</span></em>
<em><span style="color:#ff0000;"> + FullyQualifiedErrorId : PSSessionStateBroken</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-2a.png"><img class="alignnone size-full wp-image-4378" title="ps-nod32-2a" src="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-2a.png" alt="" width="640" height="134" /></a>

When trying to use <em>Enter-PSSession</em> (1 to 1 Remoting), I received the following error:

<em><span style="color:#ff0000;">Enter-PSSession : Connecting to remote server localhost failed with the following error message </span></em><em><span style="color:#ff0000;">: The WinRM client cannot process the request. The encrypted message body has an invalid format </span></em><em><span style="color:#ff0000;">and cannot be decrypted. Ensure that the service is encrypting the message body according to the </span></em><em><span style="color:#ff0000;">specifications. For more information, see the about_Remote_Troubleshooting Help topic.</span></em>
<em><span style="color:#ff0000;">At line:1 char:1</span></em>
<em><span style="color:#ff0000;">+ Enter-PSSession localhost</span></em>
<em><span style="color:#ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color:#ff0000;"> + CategoryInfo : InvalidArgument: (localhost:String) [Enter-PSSession], PSRemotingT </span></em>
<em><span style="color:#ff0000;"> ransportException</span></em>
<em><span style="color:#ff0000;"> + FullyQualifiedErrorId : CreateRemoteRunspaceFailed</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-3a.png"><img class="alignnone size-full wp-image-4379" title="ps-nod32-3a" src="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-3a.png" alt="" width="640" height="159" /></a>

When trying to run <em>Enable-PSRemoting</em>, I received another error. I was able to use cmdlets that had a computer name parameter such as <em>Get-Process</em> without issue though.

<em><span style="color:#ff0000;">&lt;f:WSManFault xmlns:f="http://schemas.microsoft.com/wbem/wsman/1/wsmanfault" Code="995" Machine="localhost</span></em><em><span style="color:#ff0000;">"&gt;&lt;f:Message&gt;WS-Management cannot process the request. The operation failed bec</span></em><em><span style="color:#ff0000;">ause of an HTTP error. The HTTP error (12152) is: The server returned an invalid or unrecognized r</span></em><em><span style="color:#ff0000;">esponse . &lt;/f:Message&gt;&lt;/f:WSManFault&gt;</span></em>
<em><span style="color:#ff0000;">At line:59 char:13</span></em>
<em><span style="color:#ff0000;">+ Set-WSManQuickConfig -force</span></em>
<em><span style="color:#ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color:#ff0000;"> + CategoryInfo : InvalidOperation: (:) [Set-WSManQuickConfig], InvalidOperationExcept</span></em><em><span style="color:#ff0000;">ion</span></em>
<em><span style="color:#ff0000;"> + FullyQualifiedErrorId : WsManError,Microsoft.WSMan.Management.SetWSManQuickConfigCommand</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-4b.png"><img class="alignnone size-full wp-image-4428" title="ps-nod32-4b" src="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-4b.png" alt="" width="640" height="162" /></a>

This took a fair amount of time to track down since I'd made several changes to my machine between the time the antivirus was updated and the time I discovered the problems. By default NOD32 version 5 does protocol filtering on HTTP connections which is evidently needed by all of the PowerShell commands that were generating errors. You could disable this protocol filtering all together (not recommended). This will require a reboot if you chose to use this method to resolve the problems:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-5.png"><img class="alignnone size-full wp-image-4363" title="ps-nod32-5" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-5.png" alt="" width="640" height="175" /></a>

My recommended way of resolving this issue is to add exceptions for PowerShell and the PowerShell ISE under Protocol filtering &gt; Excluded Applications:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-6.png"><img class="alignnone size-full wp-image-4364" title="ps-nod32-6" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-6.png" alt="" width="640" height="217" /></a>

All of the issues went away once these exceptions were added:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-7.png"><img class="alignnone size-full wp-image-4365" title="ps-nod32-7" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-7.png" alt="" width="537" height="225" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-8.png"><img class="alignnone size-full wp-image-4366" title="ps-nod32-8" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-8.png" alt="" width="419" height="120" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-9a.png"><img class="alignnone size-full wp-image-4427" title="ps-nod32-9a" src="http://mikefrobbins.com/wp-content/uploads/2012/06/ps-nod32-9a.png" alt="" width="363" height="48" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-10.png"><img class="alignnone size-full wp-image-4368" title="ps-nod32-10" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-nod32-10.png" alt="" width="452" height="74" /></a>

µ