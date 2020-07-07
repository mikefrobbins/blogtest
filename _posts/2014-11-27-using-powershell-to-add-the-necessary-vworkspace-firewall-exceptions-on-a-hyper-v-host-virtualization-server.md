---
ID: 10855
post_title: >
  Using PowerShell to add the necessary
  vWorkspace Firewall Exceptions on a
  Hyper-V Host Virtualization Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/11/27/using-powershell-to-add-the-necessary-vworkspace-firewall-exceptions-on-a-hyper-v-host-virtualization-server/
published: true
post_date: 2014-11-27 07:30:03
---
Recently while adding a Windows Server 2012 R2 Hyper-V server as a host virtualization server in a vWorkspace 8.0.1 environment, I received the following error message:

<span style="color: #ff0000;"><em>"Connect to DC service timed out. Overlapped I/O operation is in progress. (997) Retry will occur automatically if necessary."</em></span>

<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error1.jpg"><img class="alignnone size-full wp-image-10858" src="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error1.jpg" alt="vworkspace-port5203-error1" width="560" height="267" /></a>

That error message lead me to a <a href="https://support.software.dell.com/vworkspace/kb/61832" target="_blank">Dell support article</a> which stated that I needed to either disable the firewall &lt;<em>which is not going to happen on my watch</em>&gt; or open port 5203.

While the article wasn't specific, it gave me enough information to resolve the problem. In my environment, I was able to open inbound TCP port 5203 on the Hyper-V host virtualization server for the domain firewall profile since my Hyper-V host server is a domain member. You know that I accomplished that task with PowerShell &lt;<em>of course</em>&gt;.
<pre class="lang:ps decode:true">$cred = Get-Credential

Invoke-Command -ComputerName HyperV01, HyperV02 {
New-NetFirewallRule -DisplayName 'vWorkSpace Connection Broker Access' -Direction Inbound –LocalPort 5203 -Protocol TCP -Action Allow -Profile Domain
} -Credential $cred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error2.jpg"><img class="alignnone size-full wp-image-10859" src="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error2.jpg" alt="vworkspace-port5203-error2" width="875" height="108" /></a>

Once the firewall exception was made on the Hyper-V host virtualization server, I reinitialized it from within the vWorkspace management console and it completed successfully:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error3.jpg"><img class="alignnone size-full wp-image-10860" src="http://mikefrobbins.com/wp-content/uploads/2014/11/vworkspace-port5203-error3.jpg" alt="vworkspace-port5203-error3" width="599" height="70" /></a>

µ