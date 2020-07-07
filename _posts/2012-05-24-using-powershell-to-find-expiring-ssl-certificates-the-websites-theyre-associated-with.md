---
ID: 4264
post_title: 'Using PowerShell to Find Expiring SSL Certificates &#038; the Websites they&#8217;re Associated with'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/05/24/using-powershell-to-find-expiring-ssl-certificates-the-websites-theyre-associated-with/
published: true
post_date: 2012-05-24 07:30:37
---
Have you recently received a notification about an expiring SSL certificate and don't remember where all it's used at? It's generally not an issue to figure this out with normal certificates which are issued for a single name, but if it's a wildcard certificate, it could be used on lots of different websites within your organization. The following PowerShell script retrieves all of the SSL certificate's thumbprints and their expiration dates on an individual server that has IIS installed (This has only been tested on Windows Server 2008 R2).
<pre class="lang:ps decode:true">Import-Module WebAdministration
Get-ChildItem cert:\localmachine\my |
select Thumbprint, NotAfter |
sort NotAfter -desc |
ft -auto</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/sslcert11.png"><img class="alignnone size-full wp-image-4268" title="sslcert11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/sslcert11.png" width="456" height="143" /></a>

Luckily with a wildcard certificate, the thumbprint will be the same for it across all servers. The code below can be wrapped inside of <em>Invoke-Command</em> to run it remotely against multiple servers to determine all the websites the certificate is used on.
<pre class="lang:ps decode:true">Get-ChildItem iis:\sslbindings |
?{$_.thumbprint -eq "DAC5BAFC7F31BE283D43496BEB3D2345097B236C"}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/05/sslcert12.png"><img class="alignnone size-full wp-image-4269" title="sslcert12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/05/sslcert12.png" width="491" height="100" /></a>

µ