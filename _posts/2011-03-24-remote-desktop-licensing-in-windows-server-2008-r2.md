---
ID: 2048
post_title: >
  Remote Desktop Licensing in Windows
  Server 2008 R2
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/03/24/remote-desktop-licensing-in-windows-server-2008-r2/
published: true
post_date: 2011-03-24 07:30:43
---
This blog article will guide you through the steps of setting up Remote Desktop Licensing or Terminal Services Licensing as it's known in previous versions of Windows Server.

You've decided to move from Windows 2003 R2 to 2008 R2 domain controllers and you want to run your terminal services licensing on the new domain controllers. You can run the licensing for all your terminal servers operating with Windows 2000 Server and newer Windows Server versions on Windows Server 2008 R2.

On the domain controllers where you want to install the Terminal Services Licensing component, open Server Manager and select “Add Roles”. Then select the “Remote Desktop Services” role and click next (twice):
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl1.png"><img class="alignnone size-full wp-image-2049" title="tsl1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl1.png" width="640" height="471" /></a>Don't worry, this will not turn your domain controller into a terminal server.

Select “Remote Desktop Licensing” and click next:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl2.png"><img class="alignnone size-full wp-image-2050" title="tsl2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl2.png" width="640" height="471" /></a>

I recommend taking a look at this <a href="http://technet.microsoft.com/en-us/library/dd560655(WS.10).aspx#BKMK_1" target="_blank">TechNet article</a> before choosing one of these options. Select the appropriate option for your environment, click next, and then click install:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl3.png"><img class="alignnone size-full wp-image-2051" title="tsl3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl3.png" width="640" height="471" /></a>

Open RD Licensing Manager. Right click the licensing server and select “Activate Server”:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl4.png"><img class="alignnone size-full wp-image-2052" title="tsl4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl4.png" width="614" height="207" /></a>

There will be a couple of screens to enter information on that is specific to your organization. Enter this information and click next on each screen. Once you’ve completed the activation process, click next and the license installation wizard will begin. If you have not purchased licenses yet, uncheck this checkmark and click finish.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl5.png"><img class="alignnone size-full wp-image-2053" title="tsl5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl5.png" width="554" height="611" /></a>

Select the type of licenses you purchased from the drop down and click next:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl6.png"><img class="alignnone size-full wp-image-2054" title="tsl6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl6.png" width="554" height="611" /></a>

There will be a couple of screens to enter information on that is specific to the licensing you purchased. Enter this information and click next on each screen. The final screen will show that the licenses have been successfully installed:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl7.png"><img class="alignnone size-full wp-image-2055" title="tsl7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl7.png" width="554" height="611" /></a>

You can now see the licenses from within RD License Manager. This server has licenses for Windows 2000 and Windows 2008 Terminal Servers as shown below.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl8.png"><img class="alignnone size-full wp-image-2056" title="tsl8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl8.png" width="640" height="108" /></a>

If you have existing licenses for Windows 2003 terminal servers, you would just install them using the same process and then they will show up in RD License Manager:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl12.png"><img class="alignnone size-full wp-image-2071" title="tsl12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl12.png" width="640" height="58" /></a>

If a yellow warning sign is shown next to the server name, the remote desktop licensing service will need to be restarted before the server will start issuing licenses:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl10.png"><img class="alignnone size-full wp-image-2065" title="tsl10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl10.png" width="464" height="168" /></a>

To restart the service, open an administrative command prompt and run:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl11.png"><img class="alignnone size-full wp-image-2066" title="tsl11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl11.png" width="509" height="151" /></a>
<pre class="lang:batch decode:true">net stop termservlicensing &amp;&amp; net start termservlicensing</pre>
On the actual terminal server, make sure the license mode is set to the type of licenses you have installed on the licensing server. It is preferable to set this option with a GPO.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl9.png"><img class="alignnone size-full wp-image-2057" title="tsl9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/03/tsl9.png" width="555" height="256" /></a>

µ