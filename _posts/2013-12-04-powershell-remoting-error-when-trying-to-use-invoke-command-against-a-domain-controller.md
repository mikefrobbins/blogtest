---
ID: 8713
post_title: >
  PowerShell Remoting Error When Trying to
  use Invoke-Command Against a Domain
  Controller
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/12/04/powershell-remoting-error-when-trying-to-use-invoke-command-against-a-domain-controller/
published: true
post_date: 2013-12-04 07:30:39
---
I wrote a blog article last year titled "<a href="http://mikefrobbins.com/2012/06/21/welcome-to-powershell-hell/" target="_blank">Welcome to PowerShell Hell</a>" where the Antivirus I was using on my workstation had caused issues when trying to connect to remote machines with PowerShell. I recently reloaded my workstation with Windows 8.1 and reloaded the Antivirus which is now at version 7 (Eset NOD32). Remote connectivity via PowerShell worked without issue and without having to change any settings except when trying to use the <em>Invoke-Command</em> cmdlet against domain controllers which I find to be very strange.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 {Get-Process} -Credential (Get-Credential)</pre>
Here's the error that was received when attempting to use the PowerShell remoting <em>Invoke-Command</em> cmdlet against a domain controller:

<em><span style="color: #ff0000;">[dc01] Connecting to remote server dc01 failed with the following error message : The WinRM client cannot process</span></em>
<em> <span style="color: #ff0000;">the request. It cannot determine the content type of the HTTP response from the destination computer. The content type</span></em>
<em> <span style="color: #ff0000;">is absent or invalid. For more information, see the about_Remote_Troubleshooting Help topic.</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : OpenError: (dc01:String) [], PSRemotingTransportException</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : -2144108297,PSSessionStateBroken</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/icm-dcerror1.png"><img class="alignnone size-full wp-image-8714" src="http://mikefrobbins.com/wp-content/uploads/2013/12/icm-dcerror1.png" alt="icm-dcerror1" width="877" height="167" /></a>

The solution was to exclude PowerShell.exe and PowerShell_ise.exe from the protocol filtering in the Antivirus software as shown in the following image:

<a href="http://mikefrobbins.com/wp-content/uploads/2013/12/icm-dcerror2.png"><img class="alignnone size-full wp-image-8715" src="http://mikefrobbins.com/wp-content/uploads/2013/12/icm-dcerror2.png" alt="icm-dcerror2" width="809" height="517" /></a>

µ