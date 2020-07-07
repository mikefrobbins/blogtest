---
ID: 1203
post_title: >
  Multipath I/O Installation on Windows
  2008 R2 Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/08/26/multipath-io-installation-on-windows-2008-r2-server-core/
published: true
post_date: 2010-08-26 19:00:06
---
To determine if the <a href="http://en.wikipedia.org/wiki/Multipath_I/O" target="_blank">Multipath I/O</a> feature has been installed, login to your core server and run oclist.exe:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio1.jpg"><img class="alignnone size-full wp-image-1207" title="mpio1" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio1.jpg" alt="" width="361" height="54" /></a>

If you already know the name of the feature or role your looking for, you can save yourself some time by piping the output of the oclist.exe command to the find.exe command. The /I parameter makes the search case insensitive.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio12.jpg"><img class="alignnone size-full wp-image-1225" title="mpio12" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio12.jpg" alt="" width="275" height="29" /></a>

To install the MultipathI/O feature, run “start/w ocsetup.exe MultipathIo”. The name of the feature is case sensitive. Running Start/W in front of the “OCSetup.exe” command will allow the installation to complete before you’re returned to a command prompt.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio2.jpg"><img class="alignnone size-full wp-image-1208" title="mpio2" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio2.jpg" alt="" width="262" height="28" /></a>

Run mpiocpl.exe:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio21.jpg"><img class="alignnone size-full wp-image-1206" title="mpio21" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio21.jpg" alt="" width="101" height="19" /></a>

Click on the “Discover Multi-Paths” tab, check the “Add support for iSCSI devices”, and click “Add”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio4.jpg"><img class="alignnone size-medium wp-image-1210" title="mpio4" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio4.jpg?w=246" alt="" width="246" height="300" /></a>

Reboot when prompted:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio5.jpg"><img class="alignnone size-medium wp-image-1211" title="mpio5" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio5.jpg?w=300" alt="" width="300" height="115" /></a>

Run “mpclaim.exe –s –d” to verify multipath connectivity:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio7.jpg"><img class="alignnone size-medium wp-image-1213" title="mpio7" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio7.jpg?w=300" alt="" width="300" height="108" /></a>

If a disk is not showing up, verify it is set to use "MPIO" in iscsicpl:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_5.jpg"><img class="alignnone size-medium wp-image-1160" title="iscsi_5" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi_5.jpg?w=300" alt="" width="300" height="152" /></a>

More details about iscsicpl can be found in my previous blog on "<a href="http://mikefrobbins.com/2010/08/19/iscsi-initiator-configuration-on-windows-2008-r2-server-core/" target="_blank">iSCSI Initiator Configuration on Windows 2008 R2 Server Core</a>"

If everything else is set correct and the device still does not show up, run “mpclaim.exe –e” to display the detected storage systems:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio8.jpg"><img class="alignnone size-medium wp-image-1214" title="mpio8" src="http://mikefrobbins.com/wp-content/uploads/2010/08/mpio8.jpg?w=300" alt="" width="300" height="38" /></a>

The load balancing policy can also be changed with the mpclaim.exe command. The default policy is "Round Robin".

<span style="text-decoration:underline;">April 16, 2011</span>
If you're using an EqualLogic SAN, I recommend installing the EqualLogic Host Integration Tool (HIT) Kit before doing any of the Multipath I/O configuration. See my "<a href="http://mikefrobbins.com/2011/04/14/multipath-io-on-server-core-with-the-equallogic-hit-kit/" target="_blank">MultiPath I/O on Server Core with the EqualLogic HIT Kit</a>" blog for more information.<span style="text-decoration:underline;">
</span>

µ