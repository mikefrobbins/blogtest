---
ID: 4207
post_title: >
  Rename an IIS Web Application with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/17/rename-an-iis-web-application-with-powershell/
published: true
post_date: 2012-05-17 07:30:26
---
Want to rename an IIS Web Application on a Windows 2008 R2 server? It's not possible from the GUI, but it's simple to accomplish using PowerShell. First, let's create a web application in an existing website and then verify it exists:
<pre class="lang:ps decode:true">Import-Module WebAdministration
New-Item -ItemType Directory -Path d:\iis\robbinsapps
New-WebApplication -Name RobbinsApps -Site MikeFRobbins -PhysicalPath d:\iis\robbinsapps -ApplicationPool "ASP.NET v4.0"
Get-ChildItem iis:\sites\mikefrobbins</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp1.png"><img class="alignnone size-full wp-image-4208" title="ren-webapp1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp1.png" width="640" height="346" /></a>

Here it is in the GUI:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp2.png"><img class="alignnone size-full wp-image-4209" title="ren-webapp2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp2.png" width="533" height="254" /></a>

Rename the web application using PowerShell and then verify it was renamed:
<pre class="lang:ps decode:true">Rename-Item 'IIS:\Sites\MikeFRobbins\RobbinsApps' 'RobbinsApps-Archive'
Get-ChildItem iis:\sites\mikefrobbins</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp3.png"><img class="alignnone size-full wp-image-4210" title="ren-webapp3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp3.png" width="640" height="151" /></a>

Here it is again in the GUI showing that it has indeed been renamed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp4.png"><img class="alignnone size-full wp-image-4211" title="ren-webapp4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/ren-webapp4.png" width="640" height="253" /></a>

µ