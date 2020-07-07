---
ID: 2757
post_title: >
  Dell EqualLogic PS4000 – Creating a
  Volume with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/08/25/dell-equallogic-ps4000-creating-a-volume-with-powershell/
published: true
post_date: 2011-08-25 07:30:53
---
Download the EqualLogic Host Integration Toolkit (HIT Kit) for Microsoft from the <a href="http://support.dell.com/equallogic" target="_blank">EqualLogic support site</a>. Install the PowerShell Tools portion of the HIT Kit on the computer you want to manage the SAN from. For a PS4000, this computer doesn't need access to the iSCSI network as long as it has connectivity to the management network.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql1.jpg"><img class="alignnone size-full wp-image-2758" title="ps-eql1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql1.jpg" width="640" height="484" /></a>

The following PowerShell script creates a 36GB thin provisioned volume named mikefrobbins with a snapshot reserve of 100%, sets a description for the volume, allows two specific IP addresses to access the volume and its snapshots, sets up a 1am snapshot schedule that takes place once per day and attempts to keep 7 snapshots as long as the total size of the snapshots doesn't exceed the snapshot reserved space.
<pre class="lang:ps decode:true">$GrpAddr = "192.168.1.1"
$VolName = "mikefrobbins"
$VolSize = "36864"
$SnapshotReserve = "100"
$Description = "C Drive for mikefrobbins WebServer"
$ThinProvision = "Yes"
$iSCSI1 = "10.0.0.1"
$iSCSI2 = "10.0.0.2"
$ACL = "volume_and_snapshot"
$SchName = "wwwDailySnapshot"
$SchType = "Daily"
$Start = "01:00AM"
$Repeat = "0"
$Count = "7"

Import-Module -name "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
New-EqlVolume -VolumeName $VolName -VolumeSizeMB $VolSize -SnapshotReservePercent `
$SnapshotReserve -VolumeDescription $Description -ThinProvision $ThinProvision
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI1 -ACLTargetType $ACL
New-EqlVolumeACL -VolumeName $VolName -InitiatorIpAddress $iSCSI2 -ACLTargetType $ACL
New-EqlSchedule -VolumeName $VolName -ScheduleName $SchName -ScheduleType $SchType `
-StartTime $Start -TimeFrequency $Repeat -KeepCount $Count
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql2.jpg"><img class="alignnone size-full wp-image-2759" title="ps-eql2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql2.jpg" width="640" height="546" /></a>

When you execute the script, enter the grpadmin credentials or the credentials of an equivalent account when prompted:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql3.jpg"><img class="alignnone size-full wp-image-2760" title="ps-eql3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql3.jpg" width="336" height="267" /></a>

The volume has been created with the settings specified in the PowerShell script:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql4.jpg"><img class="alignnone size-full wp-image-2761" title="ps-eql4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql4.jpg" width="640" height="336" /></a>

Access to the volume and its snapshots has been setup for the two specified IP addresses:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql5.jpg"><img class="alignnone size-full wp-image-2762" title="ps-eql5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql5.jpg" width="640" height="203" /></a>

The snapshot job has been created and enabled:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql6.jpg"><img class="alignnone size-full wp-image-2763" title="ps-eql6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/ps-eql6.jpg" width="640" height="222" /></a>

You could use the GUI to accomplish the same task, but GUI’s generally leave too much room for error. Using a PowerShell script allows you to create a volume exactly the same way each time with no chance of forgetting to do something like creating a snapshot job. Nothing worse than having something missed by your backup job and committing yourself to recovering it from a snapshot only to find out: “Oh yeah, I forgot to set that up”.

µ