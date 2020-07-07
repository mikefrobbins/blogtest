---
ID: 3989
post_title: >
  Set-WSManQuickConfig Error When Running
  PowerShell Enable-PSRemoting Cmdlet on
  Web Server
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/04/26/set-wsmanquickconfig-error-when-running-powershell-enable-psremoting-cmdlet-on-web-server/
published: true
post_date: 2012-04-26 07:30:20
---
I recently ran into an issue when trying to access one of my Web Servers with PowerShell remoting. Here's a simple test to show the error:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-1.png"><img class="alignnone size-full wp-image-3991" title="042412-1" src="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-1.png" alt="" width="640" height="82" /></a>

My first thought was that PowerShell remoting wasn't enabled on the target computer. So let's try to run this without remoting:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-11.png"><img class="alignnone size-full wp-image-3997" title="042412-11" src="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-11.png" alt="" width="640" height="72" /></a>

No luck with using that route either. I headed over to the web server and ran the <em>Enable-PSRemoting</em> Cmdlet and received another error:

"<span style="color:#ff0000;"><em>Set-WSManQuickConfig : The client cannot connect to the destination specified in the request. Verify that the service on the destination is running and is accepting requests. Consult the logs and documentation for the WS-Management service running on the destination, most commonly IIS or WinRM. If the destination is the WinRM service, run the following command on the destination to analyze and configure the WinRM service: "winrm quickconfig". At line:50 char:33 + Set-WSManQuickConfig &lt;&lt;&lt;&lt; -force + CategoryInfo : InvalidOperation: (:) [Set-WSManQuickConfig], InvalidOperationException + FullyQualifiedErrorId : WsManError,Microsoft.WSMan.Management.SetWSManQuickConfigCommand</em></span>"

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-2.png"><img class="alignnone size-full wp-image-3992" title="042412-2" src="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-2.png" alt="" width="640" height="198" /></a>

I tried using the <em>-Force</em> option as specified in the error message but that didn't help either. The problem was that this web server was running IIS and Apache so a registry key named "ListenOnlyList" had been added to prevent IIS from listening on all IP addresses:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-3.png"><img class="alignnone size-full wp-image-3993" title="042412-3" src="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-3.png" alt="" width="354" height="311" /></a>

The solution to this particular problem was adding the loopback IP to the listen only list which seems to be an issue with PowerShell remoting. Someone else has already logged this same issue on <a href="http://connectppe.microsoft.com/PowerShell/feedback/details/717747/enable-psremoting-fails-when-loopback-address-is-missing" target="_blank">Microsoft Connect</a>.

There are a couple of different ways to add the loopback address (127.0.0.1), either by editing the registry or by using netsh as shown in this <a href="http://support.microsoft.com/kb/954874" target="_blank">Microsoft KB article</a>. You'll need to restart the server once the changes are made (the article states to restart services, but that didn't work). You'll then be able to enable PowerShell remoting:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-4.png"><img class="alignnone size-full wp-image-3994" title="042412-4" src="http://mikefrobbins.com/wp-content/uploads/2012/04/042412-4.png" alt="" width="640" height="218" /></a>

I did run into an issue with one out of four servers where Apache wouldn't start after adding this and rebooting. It appears that IIS must be listening on all the IP addresses that Apache is not listening on (or at least that was the only difference I could find in the four servers). I had one IP addresses that neither Apache or IIS was listening on and once it was added to the ListenOnlyList for IIS, Apache, IIS, and PowerShell were all happy.

Test this and be sure to read my disclaimer on the bottom right side of this site before attempting this on a production system.

µ