---
ID: 3283
post_title: >
  Set EqualLogic PS Series SAN Volume
  ACL’s using PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/02/16/set-equallogic-ps-series-san-volume-acls-using-powershell/
published: true
post_date: 2012-02-16 07:30:22
---
Query and return the current Access Control List (ACL) for an EqualLogic PS Series SAN volume named "mikefrobbins" using PowerShell:
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Get-EqlVolumeACL -VolumeName $VolName
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl1.png"><img class="alignnone size-full wp-image-3284" title="san-acl1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl1.png" width="541" height="497" /></a>

You can see the same information using the GUI:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl1a.png"><img class="alignnone size-full wp-image-3285" title="san-acl1a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl1a.png" width="640" height="179" /></a>

Remove the current ACL’s using the following PowerShell command:
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Remove-EqlVolumeACL -VolumeName $VolName
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl2.png"><img class="alignnone size-full wp-image-3286" title="san-acl2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl2.png" width="543" height="260" /></a>

Once again, you can see the same information using the GUI:
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl2a.png"><img class="alignnone size-full wp-image-3287" title="san-acl2a" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl2a.png" width="640" height="178" /></a>

Add new ACL’s using the following PowerShell command:
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
$iSCSI1 = "10.0.0.1"
$iSCSI2 = "10.0.0.2"
$ACL = "volume_and_snapshot"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI1 -ACLTargetType $ACL
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI2 -ACLTargetType $ACL
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl3.png"><img class="alignnone size-full wp-image-3288" title="san-acl3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl3.png" width="640" height="325" /></a>

You can also combine these into one command. The following PowerShell script retuns the current ACL’s, removes them, adds new ACL’s, and then displays the new ones to confirm the change was made.
<pre class="lang:ps decode:true">$GrpAddr = "10.0.0.200"
$VolName = "mikefrobbins"
$iSCSI1 = "10.0.0.1"
$iSCSI2 = "10.0.0.2"
$ACL = "volume_and_snapshot"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Get-EqlVolumeACL -VolumeName $VolName
Remove-EqlVolumeACL -VolumeName $VolName
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI1 -ACLTargetType $ACL
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI2 -ACLTargetType $ACL
Get-EqlVolumeACL -VolumeName $VolName
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl4.png"><img class="alignnone size-full wp-image-3289" title="san-acl4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/02/san-acl4.png" width="640" height="828" /></a>

µ