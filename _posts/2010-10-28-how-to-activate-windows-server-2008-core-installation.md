---
ID: 1348
post_title: >
  How to Activate Windows Server 2008 Core
  Installation
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/10/28/how-to-activate-windows-server-2008-core-installation/
published: true
post_date: 2010-10-28 21:30:28
---
To change the serial number on your Windows Server 2008 Core Installation, run "slmgr /ipk XXXXX-XXXXX-XXXXX-XXXXX-XXXXX" where X is your new serial number.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core1.png"><img class="alignnone size-medium wp-image-1349" title="act_core1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core1.png?w=300" width="300" height="74" /></a>

Wait for the popup confirmation message that the serial number has been changed:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core2.png"><img class="alignnone size-full wp-image-1350" title="act_core2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core2.png" width="404" height="126" /></a>

Run "cscript c:\windows\system32\slmgr.vbs -ato" to activate the operating system:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core3.png"><img class="alignnone size-medium wp-image-1351" title="act_core3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2010/10/act_core3.png?w=300" width="300" height="103" /></a>

µ