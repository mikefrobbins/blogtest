---
ID: 3152
post_title: >
  Open a SharePoint Document Library with
  Windows Explorer
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/12/15/open-a-sharepoint-document-library-with-windows-explorer/
published: true
post_date: 2011-12-15 07:30:39
---
<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer1.png"><img class="alignright size-medium wp-image-3153" title="spdoc-explorer1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer1.png?w=300" width="300" height="184" /></a>When attempting to open a SharePoint document library from a machine running Windows Server 2008 or 2008 R2 by using the “Open with Explorer” button as shown in the image to the right, you receive:  “Message from webpage &gt; Your client does not support opening this list with Windows Explorer.”:<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer2.png"><img class="alignnone size-full wp-image-3154" title="spdoc-explorer2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer2.png" width="383" height="145" /></a>

To resolve this problem, enable the “Desktop Experience” feature on the machine you are attempting to open the document library from (the client machine):
<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer3.png"><img class="alignnone size-full wp-image-3155" title="spdoc-explorer3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer3.png" width="640" height="471" /></a>

You can also enable the "Desktop Experience" feature using PowerShell:
<pre class="lang:ps decode:true">Import-Module ServerManager
Add-WindowsFeature Desktop-Experience</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer4.png"><img class="alignnone size-full wp-image-3165" title="spdoc-explorer4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/12/spdoc-explorer4.png" width="630" height="184" /></a>

A restart is required once this feature is enabled.

µ