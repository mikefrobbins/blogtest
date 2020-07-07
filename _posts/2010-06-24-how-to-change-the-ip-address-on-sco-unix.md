---
ID: 962
post_title: How to change the IP Address on SCO Unix
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/06/24/how-to-change-the-ip-address-on-sco-unix/
published: true
post_date: 2010-06-24 12:02:20
---
One of my customers re-addressed their entire internal network to a new ip-address range to allow for future growth and DHCP server redundancy. They have a legacy Unix machine sitting in the corner that no one knows anything about, but they still need to access it for legacy data. I'm not a Unix guy so there may be easier ways to do this, but here's how I changed the ip-address:

Login using the root account.
Run "ifconfig -a" to determine the interface name (which is net0 in the example below):
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/ifconfig.jpg"><img class="alignnone size-full wp-image-963" title="ifconfig" src="http://mikefrobbins.com/wp-content/uploads/2010/06/ifconfig.jpg" alt="" width="622" height="111" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/ifconfig.jpg"></a>You can add an alias using "ifconfig net0 10.0.0.1 netmask 255.0.0.0 broadcast 10.255.255.255", but the alias will be removed when the system is rebooted.

To permanently change the ip-address, run "netconfig".
Tab once and press the down arrow until "SCO TCP/IP" under the actual ethernet card is selected (not the one under the loopback adapter):
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig.jpg"><img class="alignnone size-full wp-image-964" title="netconfig" src="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig.jpg" alt="" width="634" height="292" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig.jpg"></a> Tab back to the top and select "Modify protocol configuration" under "Protocol":
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig_mod_protocol.jpg"><img class="alignnone size-full wp-image-965" title="netconfig_mod_protocol" src="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig_mod_protocol.jpg" alt="" width="261" height="89" /></a>

Tab to the fields and make the necessary modifications. Tab to Ok and press enter:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/sco_tcpip_config.jpg"><img class="alignnone size-full wp-image-966" title="sco_tcpip_config" src="http://mikefrobbins.com/wp-content/uploads/2010/06/sco_tcpip_config.jpg" alt="" width="496" height="207" /></a>

Select OK:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/config_modified.jpg"><img class="alignnone size-full wp-image-967" title="config_modified" src="http://mikefrobbins.com/wp-content/uploads/2010/06/config_modified.jpg" alt="" width="573" height="125" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/config_modified.jpg"></a>Under the "Hardware" menu, select exit:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig_exit.jpg"><img class="alignnone size-full wp-image-975" title="netconfig_exit" src="http://mikefrobbins.com/wp-content/uploads/2010/06/netconfig_exit.jpg" alt="" width="258" height="122" /></a>

Select "Yes" to re-link the kernel:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/re-link_kernal.jpg"><img class="alignnone size-full wp-image-968" title="re-link_kernal" src="http://mikefrobbins.com/wp-content/uploads/2010/06/re-link_kernal.jpg" alt="" width="488" height="100" /></a>

Wait for the rebuilt to complete and choose yes:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/set_kernal_default.jpg"><img class="alignnone size-full wp-image-970" title="set_kernal_default" src="http://mikefrobbins.com/wp-content/uploads/2010/06/set_kernal_default.jpg" alt="" width="438" height="110" /></a>

I chose no on this one:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/06/kernal_rebuild.jpg"><img class="alignnone size-full wp-image-969" title="kernal_rebuild" src="http://mikefrobbins.com/wp-content/uploads/2010/06/kernal_rebuild.jpg" alt="" width="536" height="210" /></a>

Modify the ip-addresses in /etc/hosts using the vi editor
vi hosts
i = insert mode
:q! to quit without saving
:wq to quit and save changes

Reboot and then run "ifconfig -a" to verify the settings are correct.

µ