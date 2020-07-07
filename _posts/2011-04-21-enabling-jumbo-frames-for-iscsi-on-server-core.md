---
ID: 2203
post_title: >
  Enabling Jumbo Frames for iSCSI on
  Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/04/21/enabling-jumbo-frames-for-iscsi-on-server-core/
published: true
post_date: 2011-04-21 07:30:16
---
I recommend following the instructions in my “<a href="http://mikefrobbins.com/2011/04/07/rename-a-network-interface-from-the-command-line/" target="_blank">Rename a Network Interface from the Command Line</a>” so you can easily distinguish the difference in the network interfaces. Once the network interfaces are renamed, they should look similar to the ones in this image:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc1.png"><img class="alignnone size-full wp-image-2204" title="jfsc1" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc1.png" alt="" width="567" height="174" /></a>

If you attempt to ping your <a href="http://en.wikipedia.org/wiki/Storage_area_network" target="_blank">SAN</a> at this point with a 8972 byte ping (9000 bytes minus a 20 byte IP header and a 8 byte ICMP header), you’ll receive a message stating “Packet needs to be fragmented but DF set.”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc21.png"><img class="alignnone size-full wp-image-2223" title="jfsc21" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc21.png" alt="" width="467" height="150" /></a>

In this example, I am going to enabled <a href="http://en.wikipedia.org/wiki/Jumbo_frame" target="_blank">Jumbo frames</a> on the two <a href="http://en.wikipedia.org/wiki/Iscsi" target="_blank">iSCSI</a> network interfaces (iSCSI1 and iSCSI2). Run the following command to set the <a href="http://en.wikipedia.org/wiki/Maximum_transmission_unit" target="_blank">MTU</a> to 9000 on the iSCSI1 interface. Repeat for the iSCSI2 interface.
<pre class="lang:batch decode:true">netsh interface ipv4 set subinterface "iSCSI1" mtu=9000 store=persistent</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc3.png"><img class="alignnone size-full wp-image-2206" title="jfsc3" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc3.png" alt="" width="612" height="67" /></a>

Run the following command to verify the MTU has been set to 9000:
<pre class="lang:batch decode:true">netsh interface ipv4 show interface</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc4.png"><img class="alignnone size-full wp-image-2207" title="jfsc4" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc4.png" alt="" width="570" height="175" /></a>

If you rebooted at this point and tried to ping your SAN, you might think it’s working since the result is slightly different, however it’s not quite that simple.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc51.png"><img class="alignnone size-full wp-image-2224" title="jfsc51" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc51.png" alt="" width="470" height="151" /></a>

Run regedit.exe:  <a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc6.png"><img class="alignnone size-full wp-image-2209" title="jfsc6" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc6.png" alt="" width="103" height="22" /></a>

Go to HKLM&gt;System&gt;CurrentControlSet&gt;Services&gt;Tcpip&gt;Parameters&gt;Interfaces and find the key(s) that contain the IP address of your iSCSI adapters. Record the key's name:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc7.png"><img class="alignnone size-full wp-image-2210" title="jfsc7" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc7.png" alt="" width="640" height="293" /></a>

Go to HKLM&gt;CurrentControlSet&gt;Control&gt;Class&gt;{4D36E972-E325-11CE-BFC1-08002BE10318} and find the key(s) that contains the name you recorded in the previous step. This will be in the NetCfgInstanceId field. Set the "*JumboPacket" value to 9000:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc8.png"><img class="alignnone size-full wp-image-2211" title="jfsc8" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc8.png" alt="" width="640" height="466" /></a>

If you do not have a "*JumboPacket" field, then the network driver you're using does not support this feature. In that case, download the latest driver for the network adapter and install it. In my example, this procedure is being performed on a Dell PowerEdge R710 with a Broadcom four port onboard NIC and an additional add-in Broadcom dual port NIC. The drivers for these Broadcom network cards can be downloaded from their <a href="http://www.broadcom.com/support/ethernet_nic/netxtremeii.php" target="_blank">website</a>.

<em><span style="color: #ff0000;">Warning: There is an issue with the Broadcom version 14.4.8.4 drivers that could cause the network cards to become inoperable if a virtual switch already exists on your Hyper-V host server and it is running the core installation of Windows Server 2008 R2. I have only experienced this issue on Dell PowerEdge R710 Servers. I have run the same process on Dell PowerEdge 2950 Servers with the same network cards and drivers without issue. If you have a Dell PE R710, consider removing the virtual switches before installing this driver or be prepared to reload the Hyper-V host server if you experience this problem.</span></em>

Install the drivers. If the installation is done across the network, you will temporarily lose your connection.  <a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc9.png"><img class="alignnone size-full wp-image-2212" title="jfsc9" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc9.png" alt="" width="174" height="21" /></a>

I chose yes to enable the <a href="http://en.wikipedia.org/wiki/TCP_Offload_Engine" target="_blank">TCP Offload Engine (TOE)</a> support:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc10.png"><img class="alignnone size-full wp-image-2213" title="jfsc10" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc10.png" alt="" width="413" height="165" /></a>

Once the driver installation is complete, change the “*Jumbo Packet” setting in the registry field specified above and then reboot.

You should now be able to ping your SAN using a packet size of 8972:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc111.png"><img class="alignnone size-full wp-image-2225" title="jfsc111" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc111.png" alt="" width="452" height="175" /></a>

One thing to keep in mind is that Jumbo frames must be enabled for the entire path from the server to the SAN for them to be successfully transmitted and received. Be sure to verify that Jumbo frames are enabled on your iSCSI switches. All of the switches I have seen default to disabled for Jumbo frames so this option will need to be explicitly enabled:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc12.png"><img class="alignnone size-full wp-image-2220" title="jfsc12" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc12.png" alt="" width="640" height="256" /></a>

And finally, verify your SAN has Jumbo frames enabled. The EqualLogic PS4000XV defaults to having Jumbo frames enabled and there's no way I'm aware of to disable them:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc13.png"><img class="alignnone size-full wp-image-2221" title="jfsc13" src="http://mikefrobbins.com/wp-content/uploads/2011/04/jfsc13.png" alt="" width="157" height="95" /></a>

If you experience issues, I recommend checking the following to see if any updates are available: network card driver versions, switch firmware, and SAN software revision.

µ