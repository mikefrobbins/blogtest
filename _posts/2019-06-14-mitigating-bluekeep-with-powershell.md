---
ID: 17901
post_title: Mitigating BlueKeep with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2019/06/14/mitigating-bluekeep-with-powershell/
published: true
post_date: 2019-06-14 07:30:56
---
When it comes to security, most people normally approach it at one extreme or the other. Some people do nothing and don't worry about it. All I can say for those folks is good luck with that and I hope your resume is updated. Others go into full blown panic mode, especially those who don't take the time to understand vulnerabilities. Many security folks and articles on the Internet don't help much either because they often blow things out of proportion which puts many of the people in the second category into panic mode.

First, I recommend reading "<a href="https://blogs.technet.microsoft.com/msrc/2019/05/14/prevent-a-worm-by-updating-remote-desktop-services-cve-2019-0708/" target="_blank" rel="noopener noreferrer">Prevent a worm by updating Remote Desktop Services (CVE-2019-0708)</a>".

As that article states, "Vulnerable in-support systems include <strong>Windows 7, Windows Server 2008 R2, and Windows Server 2008</strong>. Vulnerable out-of-support systems include <strong>Windows 2003 and Windows XP</strong>. Customers running Windows 8 and Windows 10 are not affected by this vulnerability, and it is no coincidence that later versions of Windows are unaffected."

Unless your running the specific operating systems that are vulnerable, you have absolutely nothing to worry about &lt;period&gt;. Support for Windows XP ended in 2014 and Windows 2003 ended in 2015 so you shouldn't be running those operating systems in the first place and you're only asking for trouble if you are. Support for Windows 7 and 2008/R2 ends in January of 2020 so the number of folks running those operating systems should be on the decline.

You certainly can't patch your production systems in the middle of the day, but as the previously referenced article states "<em>There is partial mitigation on affected systems that have Network Level Authentication (NLA) enabled. The affected systems are mitigated against ‘wormable’ malware or advanced malware threats that could exploit the vulnerability, as NLA requires authentication before the vulnerability can be triggered. However, affected systems are still vulnerable to Remote Code Execution (RCE) exploitation if the attacker has valid credentials that can be used to successfully authenticate.</em>" If an attacker has valid credentials that can be used to successfully authenticate, then you've got more problems than BlueKeep to worry about.

I'll simply query all of the servers in my demo environment to determine if any of them are impacted. I can see that none of them are impacted because they're all running newer operating systems than the ones listed in the security bulletin.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql08, sql12, sql14, sql16, sql17 {
        Get-CimInstance -ClassName Win32_OperatingSystem -Property Caption |
        Select-Object -Property @{label='OSVersion';expression={$_.Caption}},
                                @{label='RDPEnabled';expression={-not([bool](
                                    Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections'
                                    ).fDenyTSConnections)}},
                                @{label='NLAEnabled';expression={[bool](
                                    Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication'
                                    ).UserAuthentication}}
} | Select-Object -Property PSComputerName, OSVersion, RDPEnabled, NLAEnabled</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep1a.jpg"><img class="alignnone size-full wp-image-17902" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep1a.jpg" alt="" width="859" height="356" /></a>

If the remote systems are running an older operating system without PowerShell installed (Windows 2008 non-R2) or an operating system that doesn't have PowerShell remoting enabled, you could use the CIM cmdlets to query them using a CIM session with the DCOM option , but querying the registry is a little more difficult with WMI. An example of this can be found in my blog article on "<a href="https://mikefrobbins.com/2017/12/07/retrieve-basic-operating-system-information-with-powershell/" target="_blank" rel="noopener noreferrer">Retrieve Basic Operating System Information with PowerShell</a>".

I'll go ahead and enable NLA (Network Level Authentication) on the systems since it should be enabled anyway and only prevents systems running Windows XP and 2003 from connecting when enabled.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName sql08, sql12 {
    Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' -Value 1
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep2a.jpg"><img class="alignnone size-full wp-image-17904" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep2a.jpg" alt="" width="859" height="101" /></a>

I'll query those two systems again to confirm the changes were successfully applied.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep3a.jpg"><img class="alignnone size-full wp-image-17905" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep3a.jpg" alt="" width="859" height="312" /></a>

Really and truly, you shouldn't be connecting to your servers with RDP unless it's what I call a jump server that's used for management purposes, and even then, you definitely should NOT allow RDP access from the Internet. Disabling terminal services, or RDP (port 3389) on your perimeter firewalls will go a long way in securing your environment and mitigating this risk. If you are using a jump server, consider segmenting your network into separate VLAN's where only specific VLAN's can connect to the jump server.

Many of the servers in this environment run the server core installation option with no GUI, so there's even less of a reason to connect to them with remote desktop. Install the necessary management tools on your workstation or a jump server and manage them remotely. I'll go ahead and disable remote desktop on the servers that it's enabled on.
<pre class="lang:ps decode:true">Invoke-Command -ComputerName sql08, sql12, sql14 {
    Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' -Value 1
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep4a.jpg"><img class="alignnone size-full wp-image-17906" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep4a.jpg" alt="" width="859" height="102" /></a>

Confirm RDP is disabled on all of the servers.

<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep5a.jpg"><img class="alignnone size-full wp-image-17907" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep5a.jpg" alt="" width="859" height="355" /></a>

Since RDP is now disabled, there's no reason for remote desktop to be allowed in the Windows firewall on these systems so I'll also disable it.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName sql08, sql12, sql14, sql16, sql17 {
    Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled False -PassThru
}</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep6a.jpg"><img class="alignnone size-full wp-image-17909" src="https://mikefrobbins.com/wp-content/uploads/2019/06/bluekeep6a.jpg" alt="" width="859" height="88" /></a>

Of course, as the previously referenced article states "<em>We strongly advise that all affected systems – irrespective of whether NLA is enabled or not – should be updated as soon as possible.</em>" Using techniques shown in this blog article to determine the operating system along with one of my previous articles "<a href="https://mikefrobbins.com/2017/05/18/use-powershell-to-determine-if-specific-windows-updates-are-installed-on-remote-servers/" target="_blank" rel="noopener noreferrer"><em>Use PowerShell to Determine if Specific Windows Updates are Installed on Remote Servers</em></a>" can assist you in determining if the patch has been installed on those systems.

µ