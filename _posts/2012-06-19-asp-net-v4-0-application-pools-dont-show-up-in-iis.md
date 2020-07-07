---
ID: 4170
post_title: 'ASP.NET v4.0 Application Pools Don&#8217;t Show Up in IIS'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/06/19/asp-net-v4-0-application-pools-dont-show-up-in-iis/
published: true
post_date: 2012-06-19 07:30:45
---
On a Windows Server 2008 R2 Machine, a default operating system installation was performed along with installing all of the Windows Updates to include the .NET Framework v4.0, then IIS was installed. On this particular server, the ASP.NET v4.0 Application Pools didn't show up automatically in IIS:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool1.png"><img class="alignnone size-full wp-image-4165" alt="iis-net4-app-pool1" src="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool1.png" width="756" height="252" /></a>

My guess is this is because the .NET Framework 4.0 was installed before IIS. To resolve this issue open a command prompt as administrator (elevated privileges if UAC is enabled), change into the .NET Framework 4.0 directory and run"<em>aspnet_regiis.exe -ir</em>":
<pre class="lang:batch decode:true">cd C:\Windows\Microsoft.NET\Framework\v4.0.30319\
aspnet_regiis.exe -ir</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool2.png"><img class="alignnone size-full wp-image-4166" alt="iis-net4-app-pool2" src="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool2.png" width="668" height="135" /></a>

Refresh IIS and the ASP.NET v4.0 Application Pools should show up:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool3.png"><img class="alignnone size-full wp-image-4167" alt="iis-net4-app-pool3" src="http://mikefrobbins.com/wp-content/uploads/2012/05/iis-net4-app-pool3.png" width="744" height="284" /></a>

µ