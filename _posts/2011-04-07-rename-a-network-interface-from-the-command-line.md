---
ID: 2104
post_title: >
  Rename a Network Interface from the
  Command Line
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/04/07/rename-a-network-interface-from-the-command-line/
published: true
post_date: 2011-04-07 07:30:21
---
While building a Hyper-V server this week, I decided to rename the network interfaces to something that would make identifying the iSCSI connections a little easier. Since the server was installed with only the core (no GUI) installation of Windows Server 2008 R2, the process had to be performed from the command line. The network interface is also commonly referred to by other names such as network adapter or network connection.

To show a list of the network interfaces, run the following command:
<pre class="lang:batch decode:true">netsh interface ipv4 show interface</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int1.png"><img class="alignnone size-full wp-image-2135" title="ren-int1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int1.png" width="573" height="140" /></a>

To rename the “Local Area Connection 5” in the image shown above to the name “iSCSI1”, use the following command:
<pre class="lang:batch decode:true">netsh interface set interface name = "Local Area Connection 5" newname = "iSCSI1"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int2.png"><img class="alignnone size-full wp-image-2136" title="ren-int2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int2.png" width="640" height="28" /></a>

Run the show interface command again to verify the name changed:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int3.png"><img class="alignnone size-full wp-image-2137" title="ren-int3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/04/ren-int3.png" width="564" height="136" /></a>

Now aren't the network interface names in the above image a lot easier to deal with than the generic ones shown in the first image?

µ