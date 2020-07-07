---
ID: 145
post_title: >
  Hyper-V Server 2008 R2 Server
  Configuration Menu
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/09/21/hyper-v-server-2008-r2-server-configuration-menu/
published: true
post_date: 2009-09-21 19:47:21
---
Problem:
You've closed the Hyper-V Server 2008 R2 Server Configuration menu and need to re-open it:

<img class="alignnone size-full wp-image-151" title="closedsconfig" src="http://mikefrobbins.com/wp-content/uploads/2009/09/closedsconfig.png" alt="closedsconfig" width="600" height="338" />

Solution:
The command to re-open this window has changed with the R2 release. The new command is sconfig.cmd, with the non-R2 version, the command was hvconfig.cmd. Start Windows Task Manager by pressing Ctrl &amp; Shift &amp; Esc. Click File&gt;New Task:

<img class="alignnone size-full wp-image-134" title="taskmanager" src="http://mikefrobbins.com/wp-content/uploads/2009/09/taskmanager1.png" alt="taskmanager" width="404" height="448" />

Type cmd.exe /k C:\Windows\system32\sconfig.cmd and click ok:

<img class="alignnone size-full wp-image-147" title="sconfigcmd" src="http://mikefrobbins.com/wp-content/uploads/2009/09/sconfigcmd.png" alt="sconfigcmd" width="413" height="222" />

You now have a new Server Configuration window:

<img class="alignnone size-full wp-image-146" title="configmenu" src="http://mikefrobbins.com/wp-content/uploads/2009/09/configmenu.jpg" alt="configmenu" width="600" height="299" />

Simply entering sconfig.cmd in the command prompt window will work, but you'll lose your command prompt window so it is a less than desirable solution.

µ