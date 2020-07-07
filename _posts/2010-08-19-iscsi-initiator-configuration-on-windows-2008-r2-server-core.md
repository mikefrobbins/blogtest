---
ID: 1140
post_title: >
  iSCSI Initiator Configuration on Windows
  2008 R2 Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/08/19/iscsi-initiator-configuration-on-windows-2008-r2-server-core/
published: true
post_date: 2010-08-19 05:30:03
---
This blog will guide you through the necessary steps of connecting the iSCSI initiator on Windows Server 2008 R2 Server Core to an iSCSI target or volume on a Storage Area Network. I prefer server core for the host operating system on my Hyper-V virtualization servers, but some things such as the iSCSI Initiator settings are easier to <a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_1.jpg"><img class="alignleft size-full wp-image-1142" title="iscsi_1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_1.jpg" width="151" height="62" /></a>configure through a GUI. Luckily Microsoft has included the iSCSI Initiator control panel applet as part of R2. Log into your server and launch iscsicpl.exe.

The first time this command is run, you will receive the message shown below. Click “Yes” to start the iSCSI service and set it to start automatically each time the server restarts:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_2.jpg"><img class="alignnone size-full wp-image-1156" title="iscsi_2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_2.jpg" width="420" height="169" /></a>

It will take a few seconds for the service to start and then the interface shown below will load. Click the “Discovery” tab at the top and then the “Discover Portal” button:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_3.jpg"><img class="alignnone size-medium wp-image-1157" title="iscsi_3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_3.jpg?w=213" width="213" height="300" /></a>

Enter the IP Address or DNS Name of your SAN and click “OK”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_35.jpg"><img class="alignnone size-full wp-image-1158" title="iscsi_35" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_35.jpg" width="388" height="229" /></a>

Select the “Targets” tab, click the “Refresh” button, select the iSCSI target, and click the “Connect” button:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_4.jpg"><img class="alignnone size-medium wp-image-1159" title="iscsi_4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_4.jpg?w=213" width="213" height="300" /></a>

Only check the “Enable multi-path” check mark if the Multipath IO feature has been previously installed on the server. This option allows multiple iSCSI network paths from your server to your SAN to be active simultaneously. Click “OK”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_5.jpg"><img class="alignnone size-full wp-image-1160" title="iscsi_5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_5.jpg" width="420" height="214" /></a>

The iSCSI target should now show that it is connected:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_6.jpg"><img class="alignnone size-medium wp-image-1161" title="iscsi_6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_6.jpg?w=213" width="213" height="300" /></a>

Select the “Favorite Targets” tab and verify the iSCSI target shows up:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_7.jpg"><img class="alignnone size-medium wp-image-1162" title="iscsi_7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_7.jpg?w=213" width="213" height="300" /></a>

Select the “Volumes and Devices” tab, click the “Auto Configure” button, and click “OK”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_8.jpg"><img class="alignnone size-medium wp-image-1163" title="iscsi_8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_8.jpg?w=213" width="213" height="300" />
</a>The iSCSI initiator portion of the configuration is now complete.

Enable "Allow MMC Remote Management" as shown in my "<a href="http://mikefrobbins.com/2009/09/21/remote-management-of-hyper-v-server-or-server-core/" target="_blank">Remote Management of Hyper-V Server or Server Core</a>" blog.

From a machine that is running a full GUI version of an operating system (Vista or higher), launch the “Computer Management” MMC and remotely manage your core server. In "Disk Management", locate the "Unknown" disk:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_9.jpg"><img class="alignnone size-full wp-image-1164" title="iscsi_9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_9.jpg" width="108" height="43" /></a>

Right click the Disk that shows up as "Unknown" and select “Online”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_10.jpg"><img class="alignnone size-full wp-image-1165" title="iscsi_10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_10.jpg" width="86" height="47" /></a>

Right click the same Disk again and select “Initialize Disk”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_11.jpg"><img class="alignnone size-full wp-image-1166" title="iscsi_11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_11.jpg" width="89" height="55" /></a>

Leave the defaults selected unless the disk is over 2Tb (<a href="http://en.wikipedia.org/wiki/Terabyte" target="_blank">Terabytes</a>) or expected to grow over 2Tb in the future. I chose a “<a href="http://en.wikipedia.org/wiki/GUID_Partition_Table" target="_blank">GPT</a>” type disk since the iSCSI target I am mounting is 2Tb and may grow larger in the future:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_12.jpg"><img class="alignnone size-medium wp-image-1167" title="iscsi_12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_12.jpg?w=300" width="300" height="223" /></a>

Right click the "Unallocated" space and select “New Simple Volume”. From this point forward, any server admin should be able to handle it since it's just like an internal disk.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_13.jpg"><img class="alignnone size-full wp-image-1168" title="iscsi_13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_13.jpg" width="218" height="51" /></a>

Next week I'll be writing a blog on Multipath IO for Windows Server 2008 Server Core which will be available on Thursday (August 26, 2010).

<span style="text-decoration: underline;">January 27, 2011</span>
The  "Volumes and Devices" tab in the iSCSI control panel applet should actually show drive letters (unless you're mapping the volume as a folder) and must be configured after completing the drive setup inside the operating system. From what I've found, this helps the OS keep the correct drive letter mapped to the SAN volume during restarts, etc.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/01/iscsi_81.jpg"><img class="alignnone size-medium wp-image-1773" title="iscsi_81" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/01/iscsi_81.jpg?w=212" width="212" height="300" /></a>

<span style="text-decoration: underline;">April 16, 2011</span>
If you're using an EqualLogic SAN, I recommend installing the EqualLogic Host Integration Tool (HIT) Kit before doing any of the iSCSI configuration. See my "<a href="http://mikefrobbins.com/2011/04/14/multipath-io-on-server-core-with-the-equallogic-hit-kit/" target="_blank">MultiPath I/O on Server Core with the EqualLogic HIT Kit</a>" blog for more information.<span style="text-decoration: underline;">
</span>

µ