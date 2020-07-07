---
ID: 3304
post_title: >
  Take an EqualLogic PS Series SAN Volume
  Offline or Bring it Online with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/12/02/take-an-equallogic-ps-series-san-volume-offline-or-bring-it-online-with-powershell/
published: true
post_date: 2013-12-02 07:30:44
---
You have a volume on your EqualLogic PS Series SAN named "mikefrobbins":
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline1.png"><img class="alignnone size-full wp-image-3305" title="san-offline1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline1.png" width="640" height="174" /></a>

The EqualLogic Host Integration Tool (HIT) Kit for Microsoft which can be downloaded from <a href="http://support.equallogic.com/" target="_blank">support.equallogic.com</a> is required to complete the steps shown in this blog article.

The following PowerShell script can be used to take this particular SAN volume offline:
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Set-EqlVolume -VolumeName $VolName -OnlineStatus offline
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline2.png"><img class="alignnone size-full wp-image-3306" title="san-offline2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline2.png" width="538" height="262" /></a>

The volume is now offline:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline3.png"><img class="alignnone size-full wp-image-3309" title="san-offline3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline3.png" width="640" height="173" /></a>

Use the same script except with the –online parameter to bring the volume online:
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Set-EqlVolume -VolumeName $VolName -OnlineStatus online
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline4.png"><img class="alignnone size-full wp-image-3307" title="san-offline4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline4.png" width="541" height="260" /></a>

The volume is now back online:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline1.png"><img class="alignnone size-full wp-image-3305" title="san-offline1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-offline1.png" width="640" height="174" /></a>

µ