---
ID: 4036
post_title: >
  New PowerShell Cmdlets added with the
  EqualLogic HIT Kit version 4.0.0
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/01/new-powershell-cmdlets-added-with-the-equallogic-hit-kit-version-4-0-0/
published: true
post_date: 2012-05-01 07:30:01
---
The EqualLogic Host Integration Tool kit for Microsoft version 4.0.0 adds 12 additional PowerShell cmdlets to the 55 that existed in version 3.5.1 for a total of 67 cmdlets. The release notes state that firmware version 4.2.0 or later is required for the HIT kit v4.0.0 PowerShell tools. The syntax of several of the existing cmdlets also changed which appears to be mostly the addition of new parameters. See the release notes which are included as part of the new HIT kit that can be downloaded from the <a href="https://support.equallogic.com/secure/login.aspx" target="_blank">EqualLogic support site</a> for details about the changes to existing cmdlets.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-ise2.png"><img class="alignleft size-full wp-image-4130" title="ps-ise2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-ise2.png" width="125" height="89" /></a>You could figure out what additional cmdlets were added by reading the release notes, but what fun would that be? We're talking PowerShell here so why not use the power of PowerShell to determine what cmdlets were added?

Version 3.5.1 is currently installed. From PowerShell, import the EqlPSTools Module. Adjust the path as needed if the HIT kit was installed in a non-default location:
<pre class="lang:ps decode:true">Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-0.png"><img class="alignnone size-full wp-image-4055" title="ps-eql-35to4-0" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-0.png" width="617" height="16" /></a>

Version 3.5.1 had 55 PowerShell cmdlets as shown in the image below:
<pre class="lang:ps decode:true">(Get-Command -Module EqlPSTools).Count</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-1.png"><img class="alignnone size-full wp-image-4038" title="ps-eql-35to4-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-1.png" width="387" height="35" /></a>

Version 4.0.0 has 67 PowerShell cmdlets as shown in this image:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-2.png"><img class="alignnone size-full wp-image-4039" title="ps-eql-35to4-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-2.png" width="391" height="31" /></a>

Export a list of all the cmdlet names that are in version 3.5.1 before updating to version 4.0.0 by running the following PowerShell command:
<pre class="lang:ps decode:true">Get-Command -Module EqlPSTools | Select-Object -ExpandProperty Name | Out-File d:\eql-ps_351.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-32.png"><img class="alignnone size-full wp-image-4111" title="ps-eql-35to4-32" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-32.png" width="640" height="13" /></a>

Update the HIT kit on your computer to version 4.0.0 and then compare the saved results from version 3.5.1 to this new EqlPSTools PowerShell module to determine what's new:
<pre class="lang:ps decode:true">Compare-Object -ReferenceObject (Get-Content d:\eql-ps_351.txt) -DifferenceObject (Get-Command -Module EqlPSTools | Select-Object -ExpandProperty Name)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-42.png"><img class="alignnone size-full wp-image-4112" title="ps-eql-35to4-42" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/04/ps-eql-35to4-42.png" width="640" height="203" /></a>

The 12 PowerShell cmdlets shown above are the new ones added in version 4.0.0. Now wasn't that easy and a lot more fun than reading release notes to determine the new cmdlets? Still using the GUI to manage your EqualLogic SAN? Give PowerShell a try!

µ