---
ID: 214
post_title: >
  Associate SharePoint Documents with
  Application Specific Icons
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/11/20/associate-specific-icons-with-sharepoint-document-types/
published: true
post_date: 2009-11-20 11:59:05
---
Problem:
You can’t tell what types of documents are in a SharePoint document library because they show a blank icon:
<a href="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-icon1.png"><img class="alignnone size-medium wp-image-216" title="sp-icon1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-icon1.png?w=300" width="300" height="128" /></a>

Solution:
Add an icon for the document types. For pdf’s, download the small 17x17 pdf icon from Adobe: <a href="http://www.adobe.com/misc/linking.html">http://www.adobe.com/misc/linking.html</a>. Save the icon to the “C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\12\TEMPLATEIMAGES” folder on your SharePoint server.

As a best practice, make a copy of the "DOCICON.XML" file located in “c:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\12\TEMPLATEXML” on your SharePoint server before modifying it in the step below. This will allow you to easily get back to where you started (without having to resort to restoring the file from tape).

Add &lt;Mapping Key="pdf" Value="pdficon_small.gif"/&gt; to the &lt;ByExtension&gt; section of the “DOCICON.XML” file. As a best practice, add the value in the correct alphabetically order based on the file extension.

Run iisreset.exe for the changes to take effect. You now have a specific icon for pdf documents:

<a href="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-icon2.png"><img class="alignnone size-medium wp-image-217" title="sp-icon2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-icon2.png?w=300" width="300" height="127" /></a>

µ