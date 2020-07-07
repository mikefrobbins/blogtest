---
ID: 2819
post_title: >
  Resolving SharePoint 2010 PDF Issues
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2011/09/01/resolving-sharepoint-2010-pdf-issues-with-powershell/
published: true
post_date: 2011-09-01 07:30:30
---
PDF’s that have been uploaded to your SharePoint 2010 document libraries do not show the correct icon and only give you the option of saving instead of opening them:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf1.jpg"><img class="alignnone size-full wp-image-2820" title="sp2010-pdf1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf1.jpg" width="640" height="163" /></a>

The following PowerShell script downloads a 17x17 GIF image from <a href="http://www.adobe.com/misc/linking.html" target="_blank">Adobe.com</a> named pdficon_small.gif, places it in the images folder under the 14 hive, associates it in the DOCICON.XML file, sets Browser File Handling to Permissive, and then runs IISReset:
<pre class="lang:ps decode:true">$14 = "C:\Program Files\Common Files\Microsoft Shared\Web Server Extensions\14"
$src = "http://www.adobe.com/images/pdficon_small.gif"
$dst = $14 + "\TEMPLATE\IMAGES\pdficon_small.gif"
$webClient = New-Object System.Net.WebClient
$webClient.DownloadFile($src, $dst)

$xml = New-Object XML
$xml.Load($14 + "\TEMPLATE\XML\DOCICON.XML")
$icoAsn = $xml.CreateElement('Mapping')
$icoAsn.SetAttribute('Key','pdf')
$icoAsn.SetAttribute("Value","pdficon_small.gif")
$xml.DocIcons.ByExtension.AppendChild($icoAsn)
$xml.save($14 + "\TEMPLATE\XML\DOCICON.XML")

Add-PSSnapin Microsoft.SharePoint.PowerShell
Get-SPWebApplication | ForEach-Object {$_.BrowserFileHandling = “Permissive”; $_.update()}
iisreset</pre>
<span class="Apple-style-span" style="color: #444444; font-family: Georgia, 'Bitstream Charter', serif; font-size: 16px; line-height: 24px; white-space: normal;"> <a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf2.jpg"><img class="alignnone size-full wp-image-2821" title="sp2010-pdf2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf2.jpg" width="640" height="486" /></a></span>

The first part of the script downloads a pdf icon from <a href="http://www.adobe.com/misc/linking.html" target="_blank">Adobe.com</a> to the Images folder:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf3.jpg"><img class="alignnone size-full wp-image-2822" title="sp2010-pdf3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf3.jpg" width="640" height="194" /></a>

The second part associates PDF's in SharePoint with this PDF GIF image icon file:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf4.jpg"><img class="alignnone size-full wp-image-2823" title="sp2010-pdf4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf4.jpg" width="482" height="177" /></a>

The third portion of the script sets the Browser File Handling to Permissive so that the PDF’s are able to be opened instead of only saved. This was implemented on an Intranet SharePoint server so I consider this decrease in security to be an acceptable risk:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf5.jpg"><img class="alignnone size-full wp-image-2824" title="sp2010-pdf5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf5.jpg" width="281" height="401" /></a>

The last thing it does is run IISReset:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf6.jpg"><img class="alignnone size-full wp-image-2825" title="sp2010-pdf6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf6.jpg" width="297" height="101" /></a>

PDF’s now have the correct icon and are allowed to be opened:
<a href="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf7.jpg"><img class="alignnone size-full wp-image-2826" title="sp2010-pdf7" alt="" src="http://mikefrobbins.com/wp-content/uploads/2011/08/sp2010-pdf7.jpg" width="640" height="289" /></a>

The PDF icon issue and information about associating it in SharePoint is documented in this <a href="http://support.microsoft.com/kb/832809" target="_blank">Microsoft KB Article</a>.

µ