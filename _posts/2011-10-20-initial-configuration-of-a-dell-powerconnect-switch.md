---
ID: 2994
post_title: >
  Initial Configuration of a Dell
  PowerConnect Switch
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/10/20/initial-configuration-of-a-dell-powerconnect-switch/
published: true
post_date: 2011-10-20 07:30:53
---
You have a new out of the box Dell PowerConnect Switch and need to assign it an IP address so you can remotely access and manage it. Connect a 9 pin serial (female to female) cable to the switch. You’ll probably need a <a href="http://www.newegg.com/Product/Product.aspx?Item=N82E16812203018" target="_blank">USB to Serial Port adapter</a> on the computer end since most computers (especially laptops) no longer have serial ports.

Open up Hyper Terminal or another terminal emulation program. Configure the connection for: Bits Per Second = 9600, Data Bits = 8, Parity = None, Stop Bits = 1, and Flow Control = None. Press enter and you should end up at a <em>console&gt;</em> prompt.

The following commands configure the switch with a username of admin, password of password, IP address of 192.168.0.10, subnet mask of 255.255.255.0, and a default gateway of 192.168.0.1:

<em>en</em>
<em> config</em>
<em> username admin password password level 15</em>
<em> int vlan 1</em>
<em> ip address 192.168.0.10 255.255.255.0</em>
<em> exit
ip default-gateway 192.168.0.1
exit
</em><em>copy run start</em>

<em></em><a href="http://mikefrobbins.com/wp-content/uploads/2011/10/dell-switch-config1.png"><img class="alignnone size-full wp-image-2996" title="dell-switch-config1" src="http://mikefrobbins.com/wp-content/uploads/2011/10/dell-switch-config1.png" alt="" width="640" height="316" /></a>

To verify the configuration, enter:

<em>en</em>
<em>show ip int vlan 1</em>

<em></em><a href="http://mikefrobbins.com/wp-content/uploads/2011/10/dell-switch-config2.png"><img class="alignnone size-full wp-image-2997" title="dell-switch-config2" src="http://mikefrobbins.com/wp-content/uploads/2011/10/dell-switch-config2.png" alt="" width="640" height="288" /></a>

Once it's connected to your network, you should be able to remotely access the switch through a web browser using the IP address that it's been assigned.

µ