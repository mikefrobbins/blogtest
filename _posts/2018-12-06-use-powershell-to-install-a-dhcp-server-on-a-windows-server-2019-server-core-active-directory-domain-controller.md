---
ID: 17418
post_title: >
  Use PowerShell to Install a DHCP Server
  on a Windows Server 2019 (Server Core)
  Active Directory Domain Controller
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/12/06/use-powershell-to-install-a-dhcp-server-on-a-windows-server-2019-server-core-active-directory-domain-controller/
published: true
post_date: 2018-12-06 07:30:35
---
You need to have an Active Directory domain in place. I'm picking up where I left off in my previous blog article "<a href="https://mikefrobbins.com/2018/11/29/use-powershell-to-create-a-new-active-directory-forest-on-windows-2019-server-core-installation-no-gui/" target="_blank" rel="noopener">Use PowerShell to Create a New Active Directory Forest on Windows 2019 Server Core Installation (no-GUI)</a>".
<blockquote>The procedure shown in this blog article is for demonstration purposes only.</blockquote>
Install the DHCP server feature.
<pre class="lang:ps decode:true">Install-WindowsFeature -Name DHCP</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver1a.jpg"><img class="alignnone size-full wp-image-17419" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver1a.jpg" alt="" width="857" height="161" /></a>

Add the DHCP scope to the server.
<pre class="lang:ps decode:true">Add-DhcpServerv4Scope -Name '192.168.129.x' -StartRange 192.168.129.101 -EndRange 192.168.129.199 -SubnetMask 255.255.255.0</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver2a.jpg"><img class="alignnone size-full wp-image-17420" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver2a.jpg" alt="" width="857" height="79" /></a>

Options can either be set at the scope level.
<pre class="lang:ps decode:true ">Set-DhcpServerv4OptionValue -ScopeID '192.168.129.0' -DNSServer 192.168.129.100 -DNSDomain mikefrobbins.com -Router 192.168.129.1</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver3a.jpg"><img class="alignnone size-full wp-image-17421" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver3a.jpg" alt="" width="857" height="79" /></a>

Or at the server level.
<pre class="lang:ps decode:true ">Set-DhcpServerv4OptionValue -DNSServer 192.168.129.0 -DNSDomain mikefrobbins.com -Router 192.168.129.1</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver4a.jpg"><img class="alignnone size-full wp-image-17422" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver4a.jpg" alt="" width="857" height="78" /></a>

Authorize the DHCP server.
<pre class="lang:ps decode:true">Add-DhcpServerInDC -DnsName dc01.mikefrobbins.com</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver5a.jpg"><img class="alignnone size-full wp-image-17423" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver5a.jpg" alt="" width="857" height="66" /></a>

Display information about the scope.
<pre class="lang:ps decode:true ">Get-DhcpServerv4Scope | Select-Object -Property *</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver6a.jpg"><img class="alignnone size-full wp-image-17424" src="https://mikefrobbins.com/wp-content/uploads/2018/11/dhcpserver6a.jpg" alt="" width="857" height="450" /></a>
<blockquote><strong>Warning:</strong> Do NOT connect a DHCP server to your production network without explicit permission from your corporate network team &lt;period&gt;.</blockquote>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/11/warning.png"><img class="alignnone size-full wp-image-17435" src="https://mikefrobbins.com/wp-content/uploads/2018/11/warning.png" alt="" width="859" height="447" /></a>

The procedure shown in this blog article was deployed to an isolated Hyper-V internal network in a test lab.

µ