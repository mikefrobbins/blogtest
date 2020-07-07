---
ID: 4681
post_title: >
  Can iSCSI Targets be Renamed on an
  EqualLogic PS Series Storage Area
  Network?
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/10/can-iscsi-targets-be-renamed-on-an-equallogic-ps-series-storage-area-network/
published: true
post_date: 2012-07-10 07:30:08
---
I received a tweet from someone a few days ago asking if it was possible to rename an iSCSI target on an EqualLogic PS Series Storage Area Network (SAN). I wasn't sure, but it was interesting enough to research and determine if it was possible or not.

Based on a screenshot provided by this person, they wanted to change the default iSCSI target name prefix so new volumes that are created have a different target prefix. It's possible to change this setting using PowerShell, but not the GUI:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target13.jpg"><img class="alignnone size-full wp-image-4737" title="change-iscsi-target13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target13.jpg" width="640" height="351" /></a>

Even though this setting change shows up in the GUI, it doesn't have any effect when creating a new volume. You still end up with the original default iSCSI target name.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target151.jpg"><img class="alignnone size-full wp-image-4746" title="change-iscsi-target151" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target151.jpg" width="640" height="297" /></a>

Ok, but what about changing the iSCSI target name on an existing volume?

After looking at the PowerShell cmdlet parameters for <em>Set-EQLVolume</em> and looking in the GUI, it doesn't appear that an iSCSI target can be renamed. The alias can be changed, but that doesn't accomplish the same thing as renaming the iSCSI target.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target1.jpg"><img class="alignnone size-full wp-image-4682" title="change-iscsi-target1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target1.jpg" width="599" height="158" /></a>

Even when creating a new volume, there's no way in PowerShell or using the GUI to specify a custom iSCSI target name. It appears to be derived from the volume name. It's possible to rename the volume after it's created, but you're stuck with the original iSCSI target name. My guess is this is the reason you would want to rename the iSCSI target.

The only way to change the iSCSI target name on an EqualLogic PS Series SAN that I've found is to clone the volume to the name you want for the volume and iSCSI target.

Take the volume offline in the OS first:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target2.jpg"><img class="alignnone size-full wp-image-4683" title="change-iscsi-target2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target2.jpg" width="593" height="179" /></a>

Then disconnect from the iSCSI target by using the iSCSI Control Panel:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target3.jpg"><img class="alignnone size-full wp-image-4684" title="change-iscsi-target3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target3.jpg" width="485" height="704" /></a>

Open PowerShell and connect to the SAN:
<pre class="lang:ps decode:true">$GrpAddr = "192.168.10.100"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target4.jpg"><img class="alignnone size-full wp-image-4685" title="change-iscsi-target4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target4.jpg" width="495" height="57" /></a>

The clone process does not copy the description, ACL's, snapshot schedule, or replication settings. Here are the settings on the original volume that we want to replicate to the new one with the exception of the volume name and iSCSI target name:
<pre class="lang:ps decode:true">$VolName = 'mikefrobbins'
$vol = Get-EqlVolume -VolumeName $VolName
$acl = Get-EqlVolumeACL -VolumeName $VolName
$sched = Get-EqlSchedule -VolumeName $VolName
New-Object -TypeName PSObject -Property @{
'Volume Name' = $vol.volumename;
'Volume Description' = $vol.volumedescription;
'ISCSI Target Name' = $vol.iscsitargetname;
'Initiator IP Address' = $acl | foreach {$_.initiatoripaddress};
'Acl Target Type' = $acl | foreach {$_.acltargettype};
'Schedule Name' = $sched.schedulename;
'Schedule Type' = $sched.scheduletype;
'Start Time' = $sched.starttime;
'Time Frequency' = $sched.timefrequency;}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target11.jpg"><img class="alignnone size-full wp-image-4720" title="change-iscsi-target11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target11.jpg" width="640" height="118" /></a>

Clone the volume using a PowerShell script similar to the following one. The current volume name is "mikefrobbins" and I'm cloning it to a volume named "mikefrobbins2" along with copying the description, ACL's, and snapshot settings from the original volume, all with PowerShell. Each of these steps would be a manually process through the GUI. Always start out by taking a snapshot of the volume before making any changes to it. The last thing this script does is remove the original volume's ACL's and set it offline to make sure nothing can access it while keeping it around for a few days just in case an old snapshot is needed.
<pre class="lang:ps decode:true">$OldVolName = 'mikefrobbins'
$NewVolName = 'mikefrobbins2'
$VolInfo = Get-EqlVolume -VolumeName $OldVolName
$ACLSetting = Get-EqlVolumeACL -VolumeName $OldVolName
$SchedSetting = Get-EqlSchedule -VolumeName $OldVolName
New-EqlSnapshot -VolumeName $OldVolName
New-EqlVolumeClone -VolumeName $OldVolName -CloneName $NewVolName -CloneDescription (
 $volInfo).VolumeDescription
New-EqlVolumeACL -VolumeName $NewVolName -InitiatorIpAddress ($ACLSetting[0]
 ).InitiatorIpAddress -ACLTargetType ($ACLSetting[0]).AclTargetType
New-EqlVolumeACL -VolumeName $NewVolName -InitiatorIpAddress ($ACLSetting[1]
 ).InitiatorIpAddress -ACLTargetType ($ACLSetting[1]).AclTargetType
New-EqlSchedule -VolumeName $NewVolName -ScheduleName ($SchedSetting).ScheduleName -ScheduleType (
 $SchedSetting).ScheduleType -StartTime ($SchedSetting).StartTime -TimeFrequency ($SchedSetting
 ).TimeFrequency -KeepCount ($SchedSetting).KeepCount
Remove-EqlSchedule -VolumeName $OldVolName
Remove-EqlVolumeACL -VolumeName $OldVolName
Set-EqlVolume -VolumeName $OldVolName -OnlineStatus 'offline'</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target52.jpg"><img class="alignnone size-full wp-image-4703" title="change-iscsi-target52" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target52.jpg" width="640" height="327" /></a>

The original volume should be removed at a later date. I covered removing volumes in the "<a href="http://blogs.technet.com/b/heyscriptingguy/archive/2012/06/28/use-powershell-to-manage-an-equallogic-san.aspx" target="_blank">Use PowerShell to Manage an EqualLogic SAN</a>" guest blog that I wrote for the Hey, Scripting Guy! Blog a few weeks ago.

Now to verify the settings have been copied to the new volume that were previously on the old one. I'm running the same script as before except with $VolName = 'mikefrobbins2':

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target12.jpg"><img class="alignnone size-full wp-image-4721" title="change-iscsi-target12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target12.jpg" width="640" height="115" /></a>

From the iSCSI Control Panel, connect to the new iSCSI target:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target6.jpg"><img class="alignnone size-full wp-image-4687" title="change-iscsi-target6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target6.jpg" width="485" height="704" /></a>

Remove the old iSCSI target from the list on the "Favorite Targets" tab:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target9.jpg"><img class="alignnone size-full wp-image-4705" title="change-iscsi-target9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target9.jpg" width="485" height="704" /></a>

Verify the volume is online in the OS and that it has the correct drive letter assigned to it:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target7.jpg"><img class="alignnone size-full wp-image-4688" title="change-iscsi-target7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target7.jpg" width="592" height="96" /></a>

From the iSCSI control panel, click the "Auto Configure" button on the "Volume and Devices" tab so the correct drive letter in the OS is mapped to the iSCSI target each time the server is restarted:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target10.jpg"><img class="alignnone size-full wp-image-4706" title="change-iscsi-target10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target10.jpg" width="485" height="704" /></a>

Disconnect from the SAN when you're finished:
<pre class="lang:ps decode:true">Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target8.jpg"><img class="alignnone size-full wp-image-4693" title="change-iscsi-target8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/change-iscsi-target8.jpg" width="471" height="27" /></a>

µ