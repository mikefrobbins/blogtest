---
ID: 159
post_title: >
  Remote Management of Hyper-V Server or
  Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/21/remote-management-of-hyper-v-server-or-server-core/
published: true
post_date: 2009-09-21 20:16:25
---
Problem:
You receive an error Virtual Disk Manager "The RPC server is unavailable" when attempting to remotely manage Hyper-V Server 2008 or Windows Server 2008 Core Installation:

<a href="http://mikefrobbins.com/wp-content/uploads/2009/09/rpcserver.jpg"><img class="alignnone size-full wp-image-158" src="http://mikefrobbins.com/wp-content/uploads/2009/09/rpcserver.jpg" alt="rpcserver" width="600" height="429" /></a>

Solution:
Run the following command on the client and on the server:
<pre class="lang:batch decode:true">netsh advfirewall firewall set rule group="Remote Volume Management" new enable=yes</pre>
You have the option of using the Server Configuration menu on the server side if your using Hyper-V Server 2008. Select option 4, Configure Remote Management:

<img class="alignnone size-full wp-image-146" title="configmenu" src="http://mikefrobbins.com/wp-content/uploads/2009/09/configmenu.jpg" alt="configmenu" width="600" height="299" />

Select option 1, Allow MMC Remote Management:

<img class="alignnone size-full wp-image-160" title="remotemanage" src="http://mikefrobbins.com/wp-content/uploads/2009/09/remotemanage.png" alt="remotemanage" width="600" height="299" />

µ