---
ID: 2270
post_title: >
  Configure the Page File Size on Windows
  2008 Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/05/12/configuring-the-page-file-on-server-core/
published: true
post_date: 2011-05-12 07:30:01
---
I recently ran into an issue where I couldn't start any additional virtual machines on a Hyper-V server that was running Windows Server 2008 R2 Enterprise Edition - Core Installation (no GUI). After a little research, I determined that the operating system had created a pagefile of over 100 gigabytes in size which was using up the majority of the <a href="http://en.wikipedia.org/wiki/Direct-attached_storage" target="_blank">DAS</a> in the server. The server has 96GB of RAM which is the reason why the operating system automatically configured such a large pagefile. The VM's are located on a SAN, but by default Hyper-V places a file on local disk that is the size of the VM's configured memory when the VM is started. I've also seen where a server will automatically move the pagefile from say "C" drive to "D" drive on boot if it wants more space than is available on "C" drive. I recommend manually configuring the pagefile to keep this from happening and possibly creating problems such as VM's that have been running fine being unable to start after a host server restart.

To manually configure the pagefile settings, first turn off automatic management of the pagefile using the following command:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/core-pagefile1.png"><img class="alignnone size-full wp-image-2288" title="core-pagefile1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/core-pagefile1.png" width="640" height="87" /></a>
<pre class="lang:batch decode:true">wmic computersystem where name="%computername%" set AutomaticManagedPagefile=False</pre>
Then set the pagefile location and size using the following command. In the example below, a 10GB pagefile is created and placed in the root of the "C" drive.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/05/core-pagefile2.png"><img class="alignnone size-full wp-image-2289" title="core-pagefile2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/05/core-pagefile2.png" width="640" height="89" /></a>
<pre class="lang:batch decode:true">wmic pagefileset where name="C:\\pagefile.sys" set InitialSize=10240,MaximumSize=10240</pre>
There are plenty of best practice documents on paging file size and location available on the Internet so I won't cover those topics.

µ