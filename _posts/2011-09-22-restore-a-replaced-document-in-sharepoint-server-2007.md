---
ID: 2853
post_title: >
  Restore a Replaced Document in
  SharePoint Server 2007
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/09/22/restore-a-replaced-document-in-sharepoint-server-2007/
published: true
post_date: 2011-09-22 07:30:51
---
A few weeks ago I had someone ask me about restoring a single Excel spreadsheet in Microsoft Office SharePoint Server 2007 (MOSS).  The spreadsheet had been overwritten by uploading another one in its place with the same file name. Versioning was not turned on in this document library. The spreadsheet that needed to be restored was not in the user's or admin's recycle bin. I guess that's because it wasn't actually deleted. I decided that if the data is saved in the SharePoint content database as a <a href="http://en.wikipedia.org/wiki/Binary_large_object" target="_blank">blob</a>, that there must be some way to extract that blob from a restored copy of the database without it needing to be connected to a web front end server.

<span style="text-decoration: underline;">Solution:</span>
Restore a backup of the content database from the night before the item was replaced to a test database server. The AllDocs table contains two columns that are of interest when looking for a document. The DirName column is the Document Library location and the LeafName is the actual name of the items in the document library. Once you locate the item you want to restore, you'll need to join the AllDocs table to the AllDocStreams table on the id column to retrieve the item's content. The actual data of an item is stored in the AllDocStreams table in a column named content. You should be able to extract the data using whatever method you're familiar with (Visual Studio, BCP, or VBScript). I used VBScript since it seemed to be the easiest:
<pre class="lang:vb decode:true">Set cn = CreateObject("ADODB.Connection")
Set rs = CreateObject("ADODB.Recordset")
cn.Open "Provider=SQLOLEDB;data Source=DBServerName;Initial Catalog=SP_Content_RestoredDB;Trusted_Connection=yes"
Set rs = cn.Execute("select Content from AllDocStreams join AllDocs on AllDocStreams.Id = AllDocs.Id where AllDocs.LeafName = 'Spreadsheet.xlsx'")
Set mstream = CreateObject("ADODB.Stream")
mstream.Type = 1
mstream.Open
mstream.Write rs.Fields("Content").Value
mstream.SaveToFile "D:\Spreadsheet.xlsx", 2
rs.Close
cn.Close</pre>
µ