---
ID: 1104
post_title: >
  Dell EqualLogic PS4000 – Creating a
  Volume
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/08/12/dell-equallogic-ps4000-creating-a-volume/
published: true
post_date: 2010-08-12 19:45:51
---
You've followed the steps in my "<a href="http://mikefrobbins.com/2010/08/05/initial-configuration-of-a-dell-equallogic-ps4000xv-san/" target="_blank">Initial Configuration of a Dell EqualLogic PS4000 SAN</a>" <a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_0.jpg"><img class="alignleft size-full wp-image-1105" title="volume_0" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_0.jpg" alt="" width="105" height="88" /></a>blog and now your ready to start carving up the disk space on your new Storage Area Network. Open Internet Explorer and browse to the management IP Address you assigned to your SAN group. Login with the “grpadmin” account when prompted.

On the initial screen, underneath “Activities”, click “Create Volume”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_1.jpg"><img class="alignnone size-full wp-image-1106" title="volume_1" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_1.jpg" alt="" width="613" height="305" /></a>

Enter a meaningful name and description that follows your company's naming convention:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_2.jpg"><img class="alignnone size-medium wp-image-1107" title="volume_2" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_2.jpg?w=300" alt="" width="300" height="235" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_2.jpg"></a>Enter the size of the volume to create and select either MB for <a href="http://en.wikipedia.org/wiki/Megabyte" target="_blank">Megabytes</a> or GB for <a href="http://en.wikipedia.org/wiki/Gigabyte" target="_blank">Gigabytes</a>. The volume can be a “<a href="http://en.wikipedia.org/wiki/Thin_provisioning" target="_blank">Thin provisioned volume</a>” which is similar to a dynamically expanding vhd if you’re familiar with vhd’s. I wouldn't use this option for the volume where your SQL Server databases reside, but for a file server, it would be fine.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_3.jpg"><img class="alignnone size-medium wp-image-1152" title="volume_3" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_3.jpg?w=300" alt="" width="300" height="235" /></a>

Select “Restricted access” and check “Limit access by IP address”. Enter the IP Address of the dedicated iSCSI network card of the server that you plan to connect this volume to. If you have multiple network cards in the server, you can add more IP Addresses after the volume is created. Select the "Allow Simultaneous Connections" option only if this volume will be used by clustered servers.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_4.jpg"><img class="alignnone size-medium wp-image-1109" title="volume_4" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_4.jpg?w=300" alt="" width="300" height="235" /></a>

Verify the settings are correct and click "Finish":
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_5.jpg"><img class="alignnone size-medium wp-image-1110" title="volume_5" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_5.jpg?w=300" alt="" width="300" height="235" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_5.jpg"></a><a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_6.jpg"><img class="alignright size-full wp-image-1111" title="volume_6" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_6.jpg" alt="" width="124" height="197" /></a>If you have multiple network cards in your server for iSCSI network redundancy, Multipath IO, or this volume will be used for clustering, you'll need to add the IP Address of each of those additional dedicated iSCSI connections. To add them to the list that can connect to this volume, right click the volume and select “View iSCSI Setting".

You should have one IP Address listed at this point. Click "Add" to add the additonal ones. You can also modify and remove existing IP Addresses as needed in the future from this list:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_7.jpg"><img class="alignnone size-medium wp-image-1125" title="volume_7" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_7.jpg?w=300" alt="" width="300" height="229" /></a>

Click the “Limit access by IP address”, add your additional IP Address, check the “Snapshots” checkmark, and then select "OK":
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_8.jpg"><img class="alignnone size-medium wp-image-1126" title="volume_8" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_8.jpg?w=300" alt="" width="300" height="276" /></a>

You should now have two IP Addresses listed on this screen:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_7.jpg"><img class="alignnone size-medium wp-image-1125" title="volume_7" src="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_7.jpg?w=300" alt="" width="300" height="229" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/volume_7.jpg"></a>Repeat the above steps to add any additonal IP Addresses. Only add the IP Addresses to this volume for the servers dedicated iSCSI network interface card that you want to access this SAN volume.

You've now created a volume on your new Dell EqualLogic PS4000 SAN! Check back next Thursday (August 19, 2010) for a blog on how to connect Windows Server 2008 R2 Server Core to this SAN volume via the iSCSI initiator and a future blog on Multipath IO.

µ