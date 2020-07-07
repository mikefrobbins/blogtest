---
ID: 3086
post_title: >
  Reset the SharePoint 2010 Passphrase
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/12/01/reset-the-sharepoint-2010-passphrase-with-powershell/
published: true
post_date: 2011-12-01 07:30:27
---
You’re attempting to add an additional web front-end server to your SharePoint 2010 farm and find out the passphrase you've documented is incorrect or it isn't documented.
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase1.png"><img class="alignnone size-full wp-image-3087" title="reset-passphrase1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase1.png" width="640" height="15" /></a>

Resetting the passphrase is a fairly simple process. Open PowerShell and execute the following script. Enter the new passphrase when prompted:
<pre class="lang:ps decode:true"> Add-PSSnapin Microsoft.SharePoint.PowerShell
 $Passphrase = Read-Host -assecurestring "SP PassPhrase"
 Set-SPPassPhrase -PassPhrase $Passphrase –Confirm</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase2.png"><img class="alignnone size-full wp-image-3088" title="reset-passphrase2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase2.png" width="461" height="250" /></a>

Most other blogs on this subject use the code shown in the image below, but why would you want the password to be shown in clear text when it's typed into the textbox?
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase3.png"><img class="alignnone size-full wp-image-3089" title="reset-passphrase3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase3.png" width="469" height="252" /></a>

Confirm the new passphrase by typing it in again:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase4.png"><img class="alignnone size-full wp-image-3090" title="reset-passphrase4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase4.png" width="430" height="103" /></a>

Click “Yes” and the passphrase will be reset to whatever you entered in the steps above:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase5.png"><img class="alignnone size-full wp-image-3091" title="reset-passphrase5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/11/reset-passphrase5.png" width="352" height="120" /></a>

µ