---
ID: 275
post_title: >
  Remove the header row from a SharePoint
  list.
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2009/11/30/remove-the-header-row-from-a-sharepoint-list/
published: true
post_date: 2009-11-30 16:02:45
---
Problem:
You’ve added a picture column to your SharePoint “Announcements” list and you now have an unwanted column header that shows up. You only want to remove the column header for specific lists. This method can be used to hide the column header on any individual list.
<a href="http://mikefrobbins.com/wp-content/uploads/2009/11/list_w-headers.jpg"><img class="alignnone size-full wp-image-276" title="list_w-headers" alt="" src="http://mikefrobbins.com/wp-content/uploads/2009/11/list_w-headers.jpg" width="600" height="181" /></a>

Solution:
Create a custom view style that doesn't show the header row and can easily be applied to individual SharePoint list views.

As a best practice, make a copy of the "VWSTYLES.XML" file located in “C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\12\TEMPLATEGLOBALXML” on your SharePoint server before modifying it in the steps below. This will allow you to easily get back to where you started (without having to resort to restoring the file from tape).

Open the "VWSTYLES.XML" file and copy the first node starting at the beginning of &lt;ViewStyle ID="0" and ending at the end of &lt;/ViewStyle&gt;. Paste it at the end just inside the &lt;ViewStyles&gt; tag.

Change the &lt;ViewStyle ID="0" to &lt;ViewStyle ID="21" on the node you pasted or to a higher number if you have already used 21. Do not use a number less than 21 (they are the default ones). Change DisplayName="$Resources:core,ViewStyleBasicTableName;" to DisplayName="Basic, no header" and Description="$Resources:core,ViewStyleBasicTableDesc;" to Description="Basic, no header". I used "Basic, no header" for the name and description, but anything meaningful will work.

Replace &lt;HTML&gt;&lt;![CDATA["&gt;&lt;TR class="ms-viewheadertr" VALIGN=TOP&gt;]]&gt;&lt;/HTML&gt; with &lt;HTML&gt;&lt;![CDATA["&gt;&lt;TR class="ms-viewheadertr" VALIGN=TOP style="display: none;"&gt;]]&gt;&lt;/HTML&gt;. This will remove the list header as long as the list has at least one entry.

The following is needed to remove the header from a list with no entries. Replace &lt;HTML&gt;&lt;![CDATA[border=0&gt;&lt;TR class="ms-viewheadertr"&gt;]]&gt;&lt;/HTML&gt; with &lt;HTML&gt;&lt;![CDATA[border=0&gt;&lt;TR class="ms-viewheadertr" style="display: none;"&gt;]]&gt;&lt;/HTML&gt;.

Run iisreset.exe. To apply the style you’ve just created, I recommend creating a new standard view for the list named “Frontpage” or something meaningful. Only show the picture and body. Sort on created, descending. This will show the newest items at the top and the oldest items at the bottom of the list. Set the style to “Basic, no header”:

<a href="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-style.jpg"><img class="alignnone size-full wp-image-278" title="sp-style" alt="" src="http://mikefrobbins.com/wp-content/uploads/2009/11/sp-style.jpg" width="600" height="177" /></a>

Set the webpart to use the new list view you created. You now have a list without headers:
<a href="http://mikefrobbins.com/wp-content/uploads/2009/11/list_wo-headers.jpg"><img class="alignnone size-full wp-image-277" title="list_wo-headers" alt="" src="http://mikefrobbins.com/wp-content/uploads/2009/11/list_wo-headers.jpg" width="600" height="171" /></a>

µ