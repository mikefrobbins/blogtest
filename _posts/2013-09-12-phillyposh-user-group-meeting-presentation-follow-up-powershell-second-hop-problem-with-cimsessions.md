---
ID: 8212
post_title: 'PhillyPosh User Group Meeting Presentation Follow-up: PowerShell Second-Hop Problem with CimSessions'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/09/12/phillyposh-user-group-meeting-presentation-follow-up-powershell-second-hop-problem-with-cimsessions/
published: true
post_date: 2013-09-12 07:30:02
---
This blog article is a follow-up to <a href="http://powershell.org/wp/2013/09/08/phillyposh-09052013-meeting-summary/" target="_blank">my presentation for the Philadelphia PowerShell User Group</a> which was on September 5th. A video of that presentation is available on the <a href="http://www.youtube.com/channel/UCAc_ow5FIJtRpvew__9Iqzg" target="_blank">PhillyPosh YouTube Channel</a>:

[youtube=https://www.youtube.com/watch?v=KUw10Dc_igs]

I received a question towards the end of the meeting wanting to know if the Cim Cmdlets and Sessions experience the same sort of second hop authentication issue as using <em>Invoke-Command</em> where your credentials can't be re-used by a remote machine for authentication to access resources on a third machine that your command is attempting to use.

I've confirmed that the Cim Cmdlets do indeed have this issue so it isn't specifically an issue with the <em>Invoke-Command</em> cmdlet or PowerShell remoting , but possibly the way the Operating System is secured by default. To demonstrate this issue, I'll start out by running PowerShell as a domain admin and then create a CimSession to a machine named web01:
<pre class="lang:ps decode:true">$CimSession = New-CimSession -ComputerName web01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh1.png"><img class="alignnone size-full wp-image-8213" alt="cim-dh1" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh1.png" width="523" height="41" /></a>

I'm now going to backup the system event log to a local drive on the web01 server that I established the CimSession to which works without issue:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name BackupEventLog -Arguments @{
ArchiveFileName = "c:\scripts\web01_syslog_backup.evtx"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh2.png"><img class="alignnone size-full wp-image-8214" alt="cim-dh2" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh2.png" width="882" height="161" /></a>

When I attempt to backup the same event log to a network location, I receive an access denied error:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name BackupEventLog -Arguments @{
ArchiveFileName = "\\dc01\c$\tmp\web01_syslog_backup.evtx"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh3.png"><img class="alignnone size-full wp-image-8215" alt="cim-dh3" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh3.png" width="931" height="171" /></a>

CredSSP has been enabled on the client that this command is being run from and on the web01 server. For more information on enabling CredSSP see the <a href="http://technet.microsoft.com/en-us/library/hh849872.aspx" target="_blank">help for Enable-WSManCredSSP</a>.

I'll now create a CimSession to the same server, except I'll specify CredSSP as the Authentication type (Instead of the default of Kerberos):
<pre class="lang:ps decode:true">$CimSession = New-CimSession -ComputerName web01 -Authentication CredSsp -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh4.png"><img class="alignnone size-full wp-image-8216" alt="cim-dh4" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh4.png" width="909" height="69" /></a>

Running the backup to the local file system on web01 works without issue just as it previously did:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name BackupEventLog -Arguments @{
ArchiveFileName = "c:\scripts\web01_syslog_backup.evtx"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh5.png"><img class="alignnone size-full wp-image-8217" alt="cim-dh5" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh5.png" width="884" height="147" /></a>

This time though, the backup of this event log to a network location completes successfully because using CredSSP allows web01 to use our credentials to authenticate to dc01:
<pre class="lang:ps decode:true">Get-CimInstance -CimSession $CimSession Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name BackupEventLog -Arguments @{
ArchiveFileName = "\\dc01\c$\tmp\web01_syslog_backup.evtx"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh6.png"><img class="alignnone size-full wp-image-8219" alt="cim-dh6" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-dh6.png" width="883" height="149" /></a>

There was also one issue I ran into during the presentation that I said I would come back to later but didn't get a chance to so I'll cover that issue in this blog article as well:

I started off by showing the number of records that were in one of the event logs by using the <em>Get-CimInstance</em> cmdlet:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_NTEventlogFile -Filter "LogFileName = 'System'"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel1.png"><img class="alignnone size-full wp-image-8222" alt="cim-cel1" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel1.png" width="757" height="119" /></a>

I demonstrated using the <em>Invoke-CimMethod</em> cmdlet along with the <em>WhatIf</em> parameter to clear that event log:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name Clear -WhatIf</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel2.png"><img class="alignnone size-full wp-image-8223" alt="cim-cel2" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel2.png" width="771" height="95" /></a>

I then ran the same command without the <em>WhatIf</em> parameter which generated an error:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name Clear</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel3.png"><img class="alignnone size-full wp-image-8224" alt="cim-cel3" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel3.png" width="772" height="170" /></a>

As you can clearly see in the error message shown in the previous results, "clear" is not the correct method name to clear the event log (that's what happens when you try to type everything in during your presentation).

To determine what the method names are, I'll first start out by just running the <em>Get-CimClass</em> cmdlet against that particular WMI class to see what it returns. I can see there's a property named "CimClassMethods" which appears to be a collection of items:
<pre class="lang:ps decode:true">Get-CimClass -ClassName Win32_NTEventLogFile</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel4.png"><img class="alignnone size-full wp-image-8225" alt="cim-cel4" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel4.png" width="649" height="157" /></a>

To see what's in that collection of items, I'll use the <em>Select-Object</em> cmdlet with the <em>Expand-Property</em> parameter:
<pre class="lang:ps decode:true">Get-CimClass -ClassName Win32_NTEventLogFile |
Select-Object -ExpandProperty CimClassMethods</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel5.png"><img class="alignnone size-full wp-image-8226" alt="cim-cel5" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel5.png" width="644" height="326" /></a>

I can see in the previous results that "ClearEventLog" is the correct name for the method I'm looking for. Using that method to clear the event log works without issue:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_NTEventlogFile -Filter "LogFileName = 'System'" |
Invoke-CimMethod -Name ClearEventLog</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel6.png"><img class="alignnone size-full wp-image-8227" alt="cim-cel6" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel6.png" width="775" height="134" /></a>

Now the system event log only contains one record which is a record that says the event log was cleared:
<pre class="lang:ps decode:true">Get-CimInstance -ClassName Win32_NTEventlogFile -Filter "LogFileName = 'System'"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel7.png"><img class="alignnone size-full wp-image-8228" alt="cim-cel7" src="http://mikefrobbins.com/wp-content/uploads/2013/09/cim-cel7.png" width="766" height="120" /></a>

µ