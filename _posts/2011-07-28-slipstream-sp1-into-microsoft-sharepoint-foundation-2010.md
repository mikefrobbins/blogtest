---
ID: 2642
post_title: >
  Slipstream SP1 into Microsoft SharePoint
  Foundation 2010
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/07/28/slipstream-sp1-into-microsoft-sharepoint-foundation-2010/
published: true
post_date: 2011-07-28 07:30:36
---
This blog article will guide you through the process of slipstreaming SP1 into Microsoft SharePoint Foundation 2010. The process for the non-foundation edition is identical with the exception of the file names and the actual service pack file.

You’ve downloaded <a href="http://technet.microsoft.com/en-us/sharepoint/ee263910" target="_blank">SharePoint Foundation 2010</a> and <a href="http://support.microsoft.com/kb/2460058" target="_blank">SP1</a> for that specific version to D:tmp. You've created a folder (D:\sp2010) to house the installation files once they're slipstreamed.

Extract the SharePoint installation files into the D:\sp2010 folder::
<pre class="lang:batch decode:true">SharePointFoundation.exe /extract:D:\sp2010 /quiet</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/slipstream-sp2010sp1-1.png"><img class="alignnone size-full wp-image-2648" title="slipstream-sp2010sp1-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/slipstream-sp2010sp1-1.png" width="457" height="41" /></a>

Extract the SP1 files into the D:\sp2010\Updates folder:
<pre class="lang:batch decode:true">sharepointfoundation2010sp1-kb2460058-x64-fullfile-en-us.exe /extract:D:\sp2010\Updates /quiet /passive</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/07/slipstream-sp2010sp1-2.png"><img class="alignnone size-full wp-image-2649" title="slipstream-sp2010sp1-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/07/slipstream-sp2010sp1-2.png" width="640" height="53" /></a>

That’s it!

µ