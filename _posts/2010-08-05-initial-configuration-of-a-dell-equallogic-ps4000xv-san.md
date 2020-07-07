---
ID: 1005
post_title: >
  Initial Configuration of a Dell
  EqualLogic PS4000XV SAN
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/08/05/initial-configuration-of-a-dell-equallogic-ps4000xv-san/
published: true
post_date: 2010-08-05 17:59:34
---
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/unpack_40001.jpg"><img class="alignright size-full wp-image-1095" title="unpack_4000" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/unpack_40001.jpg" width="308" height="240" /></a>Unpack your new Dell EqualLogic PS4000 Storage Area Network, install it in the server rack, and cable it up. A dedicated iSCSI network that is totally separate from your normal network is required. Depending on your existing environment, you could create a separate VLAN on existing gigabit Ethernet switches to separate the iSCSI traffic from normal network traffic or you could purchase two dedicated gigabit Ethernet switches and place them in a stacked configuration or connect them via a crossover cable. I recommend separate switches unless your current switches allow you to enable <a href="http://en.wikipedia.org/wiki/Jumbo_frame" target="_blank">Jumbo frames</a> at the port or VLAN level since you'll want to enable this option for your iSCSI network, but not necessarily on your normal network.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi-network2.jpg"><img class="alignright size-full wp-image-1093" title="iscsi-network" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/iscsi-network2.jpg" width="450" height="277" /></a>For redundancy, connect the SAN controller/port 0/0 and 1/1 to switch 0 and controller/port 0/1 and 1/0 to switch 1. Depending on how much redundancy you want, you should either have two dedicated network interface cards (NIC’s) or a dedicated dual port NIC in each of your servers for the iSCSI traffic. These network cards should support <a href="http://en.wikipedia.org/wiki/TCP_Offload_Engine" target="_blank">TCP Offload Engine</a> (TOE) which allows the processing of network data to be offloaded to the network card instead of relying totally on the server's CPU. As with the SAN controllers, one connection from each server connects to switch 0 and a second connection from each connects to switch 1. In addition to being redundant, having two connections from each server will allow you to use <a href="http://en.wikipedia.org/wiki/Multipath_I/O" target="_blank">Multipath IO</a>. On the SAN, controller/port 0/2 and 1/2 are for management only and should be connected to your dedicated management network or your normal network if you so choose. I recommend not entering a default gateway on this interface which will limit the security risk by only allowing it to be managed from the subnet of your network it is plugged into. Connect each of the SAN power supplies to a separate UPS or power source. After everything is connected, power on the SAN.

Install the CD that shipped with your SAN and run the “Remote Setup Wizard”.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_1.jpg"><img class="alignnone size-full wp-image-1006" title="4000_1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_1.jpg" width="552" height="386" /></a>

Select the SAN you would like to configure:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_2.jpg"><img class="alignnone size-full wp-image-1007" title="4000_2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_2.jpg" width="552" height="386" /></a>

Enter a "Member Name" and IP address information. A dedicated IP subnet for iSCSI that is not used anywhere else on your network is required. This IP subnet is recommended to be in the <a href="http://en.wikipedia.org/wiki/Private_network" target="_blank">private ip address space</a>. Leave "Create a new group” selected unless you are adding this member to an existing group.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_3.jpg"><img class="alignnone size-full wp-image-1008" title="4000_3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_3.jpg" width="552" height="386" /></a>

Enter a “Group Name”, "IP Address", accounts information, and select a "RAID Policy". As with the above configuration, the IP address should be on your dedicated iSCSI subnet that is totally separate from your normal network. If your PS4000 is fully loaded with sixteen drives, I recommend a <a href="http://en.wikipedia.org/wiki/Nested_RAID_levels#RAID_50_.28RAID_5.2B0.29" target="_blank">RAID 50</a> RAID Policy for the best price to performance and redundancy ratio. <a href="http://en.wikipedia.org/wiki/Nested_RAID_levels#RAID_10_.28RAID_1.2B0.29" target="_blank">RAID 10</a> will perform better but at a much higher cost per terabyte. <a href="http://en.wikipedia.org/wiki/RAID_5#RAID_5" target="_blank">RAID 5</a> is not recommended since sixteen drives increases the risk of data loss to a high degree due to multiple drive failures. <a href="http://en.wikipedia.org/wiki/Standard_RAID_levels#RAID_6" target="_blank">RAID 6</a> solves the issues with RAID 5 at the cost of speed, but is not recommended after reading the "EqualLogic Eats Our Data" blog.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_4.jpg"><img class="alignnone size-full wp-image-1009" title="4000_4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_4.jpg" width="552" height="386" /></a>

Wait for this step to complete:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_5.jpg"><img class="alignnone size-full wp-image-1010" title="4000_5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_5.jpg" width="449" height="130" /></a>

I received this error on two separate PS4000 installations at two different customer sites, but it didn’t seem to affect anything:<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_6.jpg"><img class="alignnone size-full wp-image-1011" title="4000_6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_6.jpg" width="419" height="126" /></a>

Click Ok:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_7.jpg"><img class="alignnone size-full wp-image-1012" title="4000_7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_7.jpg" width="189" height="126" /></a>

You're done with the initial out of the box configuration!
<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_8.jpg"><img class="alignnone size-full wp-image-1013" title="4000_8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_8.jpg" width="552" height="386" /></a>

At this point, only one of the SAN network interfaces is enabled which is on the iSCSI network. Plug a machine into the iSCSI network, give it a static ip address in the right range, open a web browser and browse to the ip address you entered for the SAN group. Login with the "grpadmin" account when prompted. To configure the other two network interfaces, select the member name under members in the selection tree on the left and then select the "Network" tab at the top. Select each network interface under IP Configuration, set the IP address, and then enable it:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_9.jpg"><img class="alignnone size-full wp-image-1026" title="4000_9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/08/4000_9.jpg" width="640" height="307" /></a>

You can now manage your new SAN from a web browser on a machine that is connected to the same subnet as the SAN's management NIC's. Check back next Thursday (August 12th, 2010) for a blog on creating volumes (Carving up Disk Space) on a Dell EqualLogic PS4000 SAN. Future blogs will include iSCSI initiator configuration and Multipath IO installation and configuration on Windows Server 2008 R2 Server Core.

µ