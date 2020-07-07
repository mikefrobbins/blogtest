---
ID: 1443
post_title: >
  Empty Start Menu on Windows Server 2008
  Terminal Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/12/16/empty-start-menu-on-windows-server-2008-terminal-server/
published: true
post_date: 2010-12-16 05:30:08
---
Recently while assisting one of my customers who was transitioning from Windows 2003 to Windows 2008 terminal servers we experienced a problem with start menu redirection where there was nothing at all on a user’s start menu. The start menu was (empty) or<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo1.png">
</a>blank. This problem ended up being due to applying the same group policy to the <a href="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu.png"><img class="alignleft size-thumbnail wp-image-1528" title="blank-startmenu" src="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu.png?w=113" alt="" width="113" height="150" /></a><a href="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo1.png"><img class="alignright size-full wp-image-1534" title="blank-startmenu-gpo1" src="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo1.png" alt="" width="217" height="275" /></a>2008 terminal servers that was previously applied to the 2003 terminal servers. To resolve this issue the “Remove user’s folders from the Start Menu” setting that was previously enabled in the group policy object (GPO) and worked without issue on Windows Server 2003 needs to be disabled on the new Windows Server 2008 terminal servers. You can find this setting in the following location of the GPO: “User Configuration &gt; Policies &gt; Administrative Templates &gt; Start Menu and Taskbar”.
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo3.png"><img class="alignnone size-full wp-image-1536" title="blank-startmenu-gpo3" src="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo3.png" alt="" width="640" height="27" /></a>

<span style="text-decoration:underline;">Additional information about this GPO setting:</span><a href="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo4.png"><img class="alignnone size-full wp-image-1551" title="blank-startmenu-gpo4" src="http://mikefrobbins.com/wp-content/uploads/2010/12/blank-startmenu-gpo4.png" alt="" width="632" height="186" /></a>

µ