---
ID: 6835
post_title: >
  Use PowerShell to Remotely Enable
  Firewall Exceptions on Windows Server
  2012
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/02/28/use-powershell-to-remotely-enable-firewall-exceptions-on-windows-server-2012/
published: true
post_date: 2013-02-28 07:30:30
---
You're attempting to view the event logs of a couple of remote Windows Server 2012 servers that have been installed with the default installation type of server core  (No GUI).

You receive the following error when attempting to connect to these servers using the Event Viewer snapin in an MMC console:

<img class="alignnone size-full wp-image-6836" alt="set-netfirewallrule1" src="http://mikefrobbins.com/wp-content/uploads/2013/02/set-netfirewallrule1.png" width="492" height="380" />

<span style="color: #ff0000;"><em>"Computer 'DC01.MIKEFROBBINS.COM' cannot be connected. Verify</em></span>
<span style="color: #ff0000;"><em>that the network path is correct, the computer is available on the</em></span>
<span style="color: #ff0000;"><em>network, and that the appropriate Windows Firewall rules are enabled</em></span>
<span style="color: #ff0000;"><em>on the target computer.</em></span>
<span style="color: #ff0000;"><em>To enable the appropriate Windows Firewall rules on the remote</em></span>
<span style="color: #ff0000;"><em>computer, open the Windows Firewall with Advanced Security snap-in</em></span>
<span style="color: #ff0000;"><em>and enable the following inbound rules:</em></span>
<span style="color: #ff0000;"><em>COM+ Network Access (DCOM-In)</em></span>
<span style="color: #ff0000;"><em>All rules in the Remote Event Log Management group</em></span>
<span style="color: #ff0000;"><em>You can also enable these rules by using Group Policy settings for</em></span>
<span style="color: #ff0000;"><em>Windows Firewall with Advanced Security. For servers that are running</em></span>
<span style="color: #ff0000;"><em>the Server Core installation option, run the Netsh AdvFirewall</em></span>
<span style="color: #ff0000;"><em>command, or the Windows PowerShell NetSecurity module."</em></span>

One of the things that the error message in the previous image states is to enable "All rules in the Remote Event Log Management group". Well, we're in luck because it's almost like not having rights to something but having the rights to give yourself rights. Even though this firewall exception is not enabled on the remote server, PowerShell remoting is enabled by default on Windows Server 2012 so we're going to run a PowerShell script which will remotely enable all of the firewall exceptions in that rule group on the two servers.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01, sql01 {
Set-NetFirewallRule -DisplayGroup 'Remote Event Log Management' -Enabled True -PassThru |
select DisplayName, Enabled
} -Credential (Get-Credential)</pre>
<img class="alignnone size-full wp-image-6837" alt="set-netfirewallrule2" src="http://mikefrobbins.com/wp-content/uploads/2013/02/set-netfirewallrule2.png" width="640" height="535" />

The script starts out by using the PowerShell remoting <em>Invoke-Command</em> cmdlet and specifies the two server names we want to change the firewall settings on. Next, it uses the <em>Set-NetFirewallRule</em> cmdlet to enable all of the firewall exceptions that are part of the "Remote Event Log Management" display group, specifying the <em>-PassThru</em> parameter because by default the <em>Set-NetFirewallRule</em> cmdlet doesn't return any results (no objects). By returning results (objects) using the <em>-PassThru</em> parameter, we can then work with the results and pipe them to the <em>Select-Object</em> cmdlet to specify what properties we want returned in our final results. Finally, I've specified the <em>-Credential</em> parameter so alternate credentials could be specified that have the necessary permissions to make the firewall changes on the remote servers since I'm not running PowerShell as a user who has the necessary permissions.

The following image is an example of what the prompt looks like that you'll receive when using the <em>-Credential</em> parameter:

<img class="alignnone size-full wp-image-6838" alt="set-netfirewallrule3" src="http://mikefrobbins.com/wp-content/uploads/2013/02/set-netfirewallrule3.png" width="336" height="268" />

The event logs of the remote servers that we've enabled the firewall exceptions on can now be opened without error using the Event Viewer GUI tool:

<img class="alignnone size-full wp-image-6839" alt="set-netfirewallrule4" src="http://mikefrobbins.com/wp-content/uploads/2013/02/set-netfirewallrule4.png" width="640" height="230" />

µ