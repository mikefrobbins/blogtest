---
ID: 1949
post_title: >
  Installation of Exchange Server 2010
  Prerequisites
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/02/24/installation-of-exchange-server-2010-prerequisites/
published: true
post_date: 2011-02-24 07:30:19
---
It has been about six months since I transitioned one of my customers from Exchange Server 2007 to 2010 and I've found myself saying “How did I do that?” so I decided to write a blog about the process this time since I'm transitioning another customer.

In the following example, a server named email which is running the Windows Server 2008 R2 operating system has been added to the mikefrobbins.com Active Directory domain and all of the available windows updates have already been installed. The IP address information has been statically assigned.

Prepare the Active Directory schema. The first thing you should always do before attempting to update your Active Directory schema is to run DCDiag.exe to make sure there aren’t any issues that could cause problems during the update process.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p1.png"><img class="alignnone size-full wp-image-1950" title="e2010p1" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p1.png" alt="" width="508" height="767" /></a>

This scenario is being performed on an Active Directory forest that contains a single domain. Both the forest and domain are at the 2008 R2 functional level. If the schema master is running a 64bit operating system, I suggest updating the schema from it; otherwise you can update it from the member server that will be the new email server.

Log in as a member of the schema admins domain security group. If there is an existing Exchange organization in place, run <em>setup /prepareAD</em> from the drive containing the Exchange 2010 installation DVD. If there is no existing Exchange organization in this environment, run <em>setup /prepareAD /OrganizationName:”&lt;the name of your new exchange organization&gt;”</em>. In the example below, the following command is used: <em>setup /prepareAD /OrganizationName:”First Organization”</em>.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p2.png"><img class="alignnone size-full wp-image-1951" title="e2010p2" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p2.png" alt="" width="640" height="432" /></a>

Open PowerShell on the new email server and run <em>Import-Module ServerManager</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/msa3.jpg"><img class="alignnone size-full wp-image-1889" title="msa3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/msa3.jpg" alt="" width="305" height="71" /></a> <a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p2.png"></a>

For a server that will have the typical installation of Client Access, Hub Transport, and the Mailbox roles, run <em>Add-WindowsFeature NET-Framework,RSAT-ADDS,Web-Server,Web-Basic-Auth,Web-Windows-Auth,Web-Metabase,Web-Net-Ext,Web-Lgcy-Mgmt-Console,WAS-Process-Model,RSAT-Web-Server,Web-ISAPI-Ext,Web-Digest-Auth,Web-Dyn-Compression,NET-HTTP-Activation,RPC-Over-HTTP-Proxy -Restart</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p3.png"><img class="alignnone size-full wp-image-1952" title="e2010p3" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p3.png" alt="" width="640" height="603" /></a>

After the server has restarted, set the Net.Tcp Port Sharing Service to Automatic startup by running the following PowerShell command: <em>Set-Service NetTcpPortSharing -StartupType Automatic</em>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p4.png"><img class="alignnone size-full wp-image-1953" title="e2010p4" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p4.png" alt="" width="493" height="59" /></a>

<a href="http://go.microsoft.com/fwlink/?LinkId=123380" target="_blank">Download</a> and install the 2007 Office System Converter: Microsoft Filter Pack<a href="http://go.microsoft.com/fwlink/?LinkId=123380" target="_blank">
</a><a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p5.png"><img class="alignnone size-full wp-image-1954" title="e2010p5" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p5.png" alt="" width="503" height="407" /></a>

<a href="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p6.png"><img class="alignnone size-full wp-image-1955" title="e2010p6" src="http://mikefrobbins.com/wp-content/uploads/2011/02/e2010p6.png" alt="" width="406" height="205" /></a>

You are now ready to install Exchange Server 2010 using the typical installation option.

µ