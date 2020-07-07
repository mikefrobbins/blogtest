---
ID: 1419
post_title: >
  Enabling Remote Access to Device Manager
  on Server Core
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/12/02/enabling-remote-access-to-device-manager-on-server-core/
published: true
post_date: 2010-12-02 05:30:17
---
You’ve added hardware to a Windows Server 2008 R2 Core installation machine and you want to check the status of it through the GUI by using Device Manager on a remote computer. The following process allows you to access Device Manager remotely in “Read Only” mode and it assumes that you've already configured remote management on your core server through option 4 of sconfig. You're already able to access the event logs and/or services using Computer Management on a remote computer. When you attempt to access Device Manager remotely, you receive the following error:

“Unable to access the computer Make sure that this computer is on the network, has remote administration enabled, and is running the “Plug and Play” and “Remote registry” services. The error was: Access is denied.”
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc1.png"><img class="alignnone size-full wp-image-1424" title="radmsc1" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc1.png" alt="" width="466" height="229" /></a>

By default, remote access to the plug and play interface is disabled and needs to be enabled with either a GPO or through the local security policy on the core server. If you have multiple server core machines that you want to enable this on and they’re all in a domain, it’s a best practice to create an OU in your domain for the server core machines, create a GPO, and apply it to the OU. In this example since I’m in a test environment or if your server core machines are not part of a domain, you’ll need to modify the local security policy. To remotely access the local security policy on your server core machine, launch an mmc console on a remote computer, add the “Group Policy  Object Editor” mmc snap-in. Select browse:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc2.png"><img class="alignnone size-medium wp-image-1425" title="radmsc2" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc2.png?w=300" alt="" width="300" height="258" /></a>

Enter the server name of you core machine:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc3.png"><img class="alignnone size-medium wp-image-1426" title="radmsc3" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc3.png?w=300" alt="" width="300" height="221" /></a>

Go to the following location:
Computer Configuration &gt; Administrative Templates &gt; System &gt; Device Installation

Select the “Allow remote access to the Plug and Play interface”:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc4.png"><img class="alignnone size-full wp-image-1427" title="radmsc4" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc4.png" alt="" width="624" height="195" /></a>

Set it to Enabled:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc5.png"><img class="alignnone size-medium wp-image-1428" title="radmsc5" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc5.png?w=300" alt="" width="300" height="275" /></a>

Run "gpupdate.exe" on the core server. You should now be able to use computer management on a remote computer to access “Device Manager” on your server core machine. You will receive the following message stating it is in “Read-Only” mode:
<a href="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc6.png"><img class="alignnone size-full wp-image-1429" title="radmsc6" src="http://mikefrobbins.com/wp-content/uploads/2010/12/radmsc6.png" alt="" width="497" height="199" /></a>

µ