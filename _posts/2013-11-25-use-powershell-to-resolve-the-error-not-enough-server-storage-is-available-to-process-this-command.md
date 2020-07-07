---
ID: 8616
post_title: 'Use PowerShell to Resolve the Error: &#8220;Not Enough Server Storage is Available to Process this Command&#8221;'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/11/25/use-powershell-to-resolve-the-error-not-enough-server-storage-is-available-to-process-this-command/
published: true
post_date: 2013-11-25 07:30:17
---
Recently after updating the antivirus solution at one of my customer sites they started receiving the following error when attempting to access their old Windows 2003 servers:

<em> "\\Server\Share is not accessible. You might not have permission to use this network resource. Contact the administrator of this server to find out if you have access permissions. Not enough server storage is available to process this command."</em>

<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/IRPStackSize1.png"><img class="alignnone size-full wp-image-8617" alt="IRPStackSize1" src="http://mikefrobbins.com/wp-content/uploads/2013/11/IRPStackSize1.png" width="905" height="126" /></a>

The solution was to increase the IRPStackSize on the remote server as referenced in this <a href="http://support.microsoft.com/kb/285089" target="_blank">Microsoft Support article</a>. Once this registry entry is added, it does require the remote server to be restarted.

Luckily, I had previously installed PowerShell version 2 along with enabling PowerShell remoting on all of their Windows 2003 servers so I was able to use PowerShell remoting to add the entry in the registry and reboot the servers:
<pre class="wrap:false lang:ps decode:true">$cred = Get-Credential

Invoke-Command -ComputerName server01, server02, server03, server04, server05, server06 {
    New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\lanmanserver\parameters" -Name "IRPStackSize" -Value 20 -PropertyType "DWord"
    Restart-Computer -Force
} -Credential $cred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2013/11/IRPStackSize2.png"><img class="alignnone size-full wp-image-8618" alt="IRPStackSize2" src="http://mikefrobbins.com/wp-content/uploads/2013/11/IRPStackSize2.png" width="1009" height="304" /></a>

I'm looking forward to replacing all of their old Windows 2003 servers with Windows 2012 R2 during the first quarter of next year.

µ