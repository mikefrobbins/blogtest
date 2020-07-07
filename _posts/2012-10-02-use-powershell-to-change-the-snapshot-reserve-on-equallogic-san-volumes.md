---
ID: 5506
post_title: >
  Use PowerShell to Change the Snapshot
  Reserve on EqualLogic SAN Volumes
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/10/02/use-powershell-to-change-the-snapshot-reserve-on-equallogic-san-volumes/
published: true
post_date: 2012-10-02 07:30:25
---
You have a customer who has an EqualLogic PS Series Storage Area Network (SAN) where the Snapshot Reserve for many of their SAN volumes is currently set to more than 100% of the actual volume size. The SAN also has several volumes where the snapshot reserve is set to exactly 100%, other volumes where the snapshot reserve is set to less than 100%, and others that don't have or need snapshots configured at all.

This customer has recently implemented a new backup solution that uses point in time recovery similar to SAN snapshots except utilizing cheaper storage. They would like to free up some disk space that is reserved for snapshots on their SAN by changing the snapshot reserve to 100% on any volume where the snapshot reserve is currently set to greater than 100%.

The EqualLogic Host Integration Tools (HIT Kit) for Microsoft have been installed on the machine this PowerShell script is being run from. Since PowerShell version 3.0 is also installed on the machine this script is being run from, we'll take advantage of the simplified Where-Object syntax.
<pre class="lang:ps decode:true">$GrpAddr = "192.168.10.100"
Import-Module "c:\program files\EqualLogic\bin\EqlPSTools.dll"
Connect-EqlGroup -GroupAddress $GrpAddr -Credential (Get-Credential)
Get-Eqlvolume |
where snapshotreservepercent -gt 100 |
Set-EqlVolume -SnapshotReservePercent 100
Disconnect-EqlGroup -GroupAddress $GrpAddr</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/10/eql-snapshot100percent.jpg"><img class="alignnone size-full wp-image-5507" title="eql-snapshot100percent" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/10/eql-snapshot100percent.jpg" width="640" height="388" /></a>

There were 14 volumes out of possibly hundreds where this change needed to be made based on the customers requirements. Now who wants to manually dig through the GUI looking for the volumes that need to be changed and make the change to them manually? And who wants to use PowerShell to make the change as shown in this blog?

µ