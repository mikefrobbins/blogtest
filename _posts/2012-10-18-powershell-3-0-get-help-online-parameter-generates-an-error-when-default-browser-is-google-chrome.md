---
ID: 5605
post_title: >
  PowerShell 3.0 Get-Help -Online
  Parameter Generates an Error When
  Default Browser is Google Chrome
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/18/powershell-3-0-get-help-online-parameter-generates-an-error-when-default-browser-is-google-chrome/
published: true
post_date: 2012-10-18 07:30:39
---
I'm a big fan of the online version of PowerShell help. I also use Google Chrome as my default web browser. Since I've moved to Windows 8 and PowerShell 3.0, using the <em>Get-Help</em> PowerShell cmdlet with the Online parameter generates an error.

There's actually two different errors that I've seen a lot of, the first one is because the online help doesn't appear to exist and this error occurs regardless of what web browser you're using:

<em><span style="color: #ff0000;">Get-Help : The online version of this Help topic cannot be displayed because the </span></em>
<em> <span style="color: #ff0000;">Internet address (URI) of the Help topic is not specified in the command code or in the </span></em>
<em> <span style="color: #ff0000;">help file for the command.</span></em>
<em> <span style="color: #ff0000;">At line:55 char:7</span></em>
<em> <span style="color: #ff0000;">+ Get-Help @PSBoundParameters | more</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : InvalidOperation: (:) [Get-Help], PSInvalidOperationExcept </span></em>
<em> <span style="color: #ff0000;"> ion</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : InvalidOperation,Microsoft.PowerShell.Commands.GetHelpComm </span></em>
<em> <span style="color: #ff0000;"> and</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error1.png"><img class="alignnone size-full wp-image-5606" title="pshelp-error1" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error1.png" alt="" width="640" height="181" /></a>

Based on the <em>about_windows_powershell_3.0</em> help topic, there's a help URI link that has to exist:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error2.png"><img class="alignnone size-full wp-image-5607" title="pshelp-error2" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error2.png" alt="" width="625" height="269" /></a>

You can see that a value exists for the HelpUri property for the <em>Get-WindowsEdition</em> cmdlet, but not the <em>Get-KdsConfiguration</em> cmdlet. Based on searching for help on the TechNet website, my guess is the online help for that particular cmdlet doesn't exist yet.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error3.png"><img class="alignnone size-full wp-image-5608" title="pshelp-error3" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error3.png" alt="" width="531" height="180" /></a>

Based on these results, the online help should work for the <em>Get-WindowsEdition</em> cmdlet, but it generates this error:

<em><span style="color: #ff0000;">Get-Help : Launching a program to show online help failed. No program is associated to </span></em>
<em> <span style="color: #ff0000;">launch URI http://go.microsoft.com/fwlink/?LinkId=215281.</span></em>
<em> <span style="color: #ff0000;">At line:55 char:7</span></em>
<em> <span style="color: #ff0000;">+ Get-Help @PSBoundParameters | more</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : InvalidOperation: (:) [Get-Help], PSInvalidOperationExcept </span></em>
<em> <span style="color: #ff0000;"> ion</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : InvalidOperation,Microsoft.PowerShell.Commands.GetHelpComm </span></em>
<em> <span style="color: #ff0000;"> and</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error4.png"><img class="alignnone size-full wp-image-5609" title="pshelp-error4" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error4.png" alt="" width="640" height="170" /></a>

The online help for the <em>Get-WindowsFeature</em> cmdlet works without issue after changing my default web browser to Mozilla Firefox:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error5.png"><img class="alignnone size-full wp-image-5610" title="pshelp-error5" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error5.png" alt="" width="517" height="311" /></a>

It also works without issue after changing it to Internet Explorer:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error6.png"><img class="alignnone size-full wp-image-5611" title="pshelp-error6" src="http://mikefrobbins.com/wp-content/uploads/2012/10/pshelp-error6.png" alt="" width="505" height="272" /></a>

µ