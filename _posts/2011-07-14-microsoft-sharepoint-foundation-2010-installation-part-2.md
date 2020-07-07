---
ID: 2526
post_title: >
  Microsoft SharePoint Foundation 2010
  Installation – Part 2
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/07/14/microsoft-sharepoint-foundation-2010-installation-part-2/
published: true
post_date: 2011-07-14 07:30:22
---
This blog is a continuation from last weeks blog (<a href="http://mikefrobbins.com/2011/07/07/microsoft-sharepoint-foundation-2010-installation-part-1/" target="_blank">Part 1</a>). We'll jump right in where we left off. Verify you are logged into the server that you want to install SharePoint on as the sp<em>Name</em>Install account. Run the SharePoint executable (SharePointFoundation.exe) that you previously downloaded. Select "Install software prerequisites":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install13.png"><img class="alignnone size-full wp-image-2527" title="sp2010-install13" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install13.png" alt="" width="640" height="480" /></a>

With SharePoint 2010, there's no need to manually install any of the prerequisites as in previous versions of SharePoint. It installs all of them for you:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install14.png"><img class="alignnone size-full wp-image-2529" title="sp2010-install14" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install14.png" alt="" width="640" height="478" /></a>

The SharePoint server will need Internet connectivity since many of the prerequisites will be downloaded from the Internet automatically during the installation process. The prerequisite installation process takes less than ten minutes to complete on a decent machine with high speed Internet access.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install15.png"><img class="alignnone size-full wp-image-2531" title="sp2010-install15" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install15.png" alt="" width="640" height="478" /></a>

Select "Install SharePoint Foundation":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install13.png"><img class="alignnone size-full wp-image-2527" title="sp2010-install13" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install13.png" alt="" width="640" height="480" /></a>

Select "Server Farm":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install16.png"><img class="alignnone size-full wp-image-2532" title="sp2010-install16" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install16.png" alt="" width="616" height="500" /></a>

Select "Complete":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install17.png"><img class="alignnone size-full wp-image-2533" title="sp2010-install17" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install17.png" alt="" width="616" height="500" /></a>

Change the data location to your data drive. I recommend only changing the drive letter and leaving the remainder of the default path intact:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install18.png"><img class="alignnone size-full wp-image-2534" title="sp2010-install18" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install18.png" alt="" width="616" height="500" /></a>

Uncheck “Run the SharePoint Products Configuration Wizard now.” and then click "Close":
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install19.png"><img class="alignnone size-full wp-image-2535" title="sp2010-install19" src="http://mikefrobbins.com/wp-content/uploads/2011/07/sp2010-install19.png" alt="" width="616" height="500" /></a>

Leaving this checked would run the configuration wizard which does things such as add GUID’s to your database names. We'll be using PowerShell to complete the steps that the configuration wizard would normally perform.

<a href="http://mikefrobbins.com/2011/07/21/microsoft-sharepoint-foundation-2010-installation-part-3/" target="_blank">Part 3</a> of this blog article series, which will guide you through the initial configuration using PowerShell, will be posted on Thursday July 21st.

µ