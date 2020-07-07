---
ID: 4155
post_title: Create a New IIS Website with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/15/create-a-new-iis-website-with-powershell/
published: true
post_date: 2012-05-15 07:30:38
---
On a Windows Server 2008 R2 machine, IIS has already been installed and you want to create an additional website. If necessary, an additional IP Address has been added to the server also.

Use the <em>New-Website</em> PowerShell cmdlet to accomplish this task:
<pre class="lang:ps decode:true">Import-Module WebAdministration
New-Item -type directory -path d:\iis\mikefrobbins
New-Website -Name "mikefrobbins" -Port 80 -IPAddress "192.168.1.222" -PhysicalPath d:\iis\mikefrobbins -ApplicationPool "ASP.NET v4.0"</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-create-website1.png"><img class="alignnone size-full wp-image-4159" title="ps-create-website1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-create-website1.png" width="640" height="277" /></a>

You can get a list of websites running on the server by using the <em>Get-Website</em> cmdlet or by running <em>Get-ChildItem</em> on the IIS PSDrive:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-create-website2.png"><img class="alignnone size-full wp-image-4158" title="ps-create-website2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ps-create-website2.png" width="640" height="381" /></a>

µ