---
ID: 712
post_title: >
  Installing MOSS 2007 on Windows Server
  2008 R2
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2010/05/06/installing-moss-2007-on-windows-server-2008-r2/
published: true
post_date: 2010-05-06 11:55:49
---
I recently built a test machine with Windows Server 2008 R2 and Microsoft Office SharePoint Server (MOSS) 2007. Before you get started with the MOSS installation, you need to add the .Net Framework 3.5.1 feature to your server and the Web Server (IIS) role. If you do not add the Web Server role manually, the MOSS installation will add it for you, but you’ll run into difficulties later. I've previously installed MOSS 2007 and WSS 3.0 on Windows Server 2008 (non-R2) and never ran into any issues.

The first issue I ran into was that MOSS 2007 with SP2 is required to install on Windows Server 2008 R2. A non-SP version or a SP1 version will not install on Windows Server 2008 R2. You can slipstream SP2 into an ISO if you do not have access to media that already has SP2 integrated into it. If you attempt to install a non-SP2 version of MOSS 2007 on Windows Server 2008 R2, you’ll receive the following message:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/paw_moss2007.png"><img class="alignnone size-full wp-image-713" title="paw_moss2007" src="http://mikefrobbins.com/wp-content/uploads/2010/05/paw_moss2007.png" alt="" width="566" height="273" /></a>

I chose a basic installation since I wanted to quickly setup a test machine. I received several of these “Program Compatibility Assistant” messages since the basic installation installs SQL 2005. The warming states that you need to install SP3 for SQL 2005.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/pca_moss.png"><img class="alignnone size-full wp-image-714" title="pca_moss" src="http://mikefrobbins.com/wp-content/uploads/2010/05/pca_moss.png" alt="" width="566" height="275" /></a>

Watch for what I call pop-unders. This message pops up underneath the installallation screen. The installation will wait forever or until you find the pop-under and click “Run program”.

Here is where you start to see the issues of not manually adding the Web Server role to the server before installing MOSS. Running the configuration wizard at the end of the installation fails with the following:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/moss_config_failed.png"><img class="alignnone size-full wp-image-715" title="moss_config_failed" src="http://mikefrobbins.com/wp-content/uploads/2010/05/moss_config_failed.png" alt="" width="600" height="512" /></a>

The referenced error log doesn’t help much. Re-running the configuration wizard without creating a default site will complete successfully. Ignore the name of my server, it's a virtual machine I previously tested MySQL on.

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/sp_config_overwirte.png"><img class="alignnone size-full wp-image-716" title="sp_config_overwirte" src="http://mikefrobbins.com/wp-content/uploads/2010/05/sp_config_overwirte.png" alt="" width="600" height="512" /></a>

At this point, a Team Site can be created without issue, but attempting to create a Collaboration or Publishing Portal will fail. You receive the Error “The Office SharePoint Server Standard Web application features feature must be activated at the web application level before this feature can be activated.”:

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/portal_error.png"><img class="alignnone size-full wp-image-717" title="portal_error" src="http://mikefrobbins.com/wp-content/uploads/2010/05/portal_error.png" alt="" width="529" height="127" /></a>

Checking the Web Application Features shows that this feature is already Active (It is also active at the Farm level):

<a href="http://mikefrobbins.com/wp-content/uploads/2010/05/webapp_feature.png"><img class="alignnone size-full wp-image-727" title="webapp_feature" src="http://mikefrobbins.com/wp-content/uploads/2010/05/webapp_feature.png" alt="" width="600" height="133" /></a>

I found references of this issue in a <a href="http://social.technet.microsoft.com/Forums/en-US/sharepointadmin/thread/8b96663a-ed87-4ab4-bd59-33c7245a76d4" target="_blank">forum</a> on the TechNet website. The only solution I have found is to reinstall MOSS, although there may be other solutions, you can save yourself a lot of trouble by adding the Web Server role to you server before attempting to install MOSS on Windows Server 2008 R2.

µ