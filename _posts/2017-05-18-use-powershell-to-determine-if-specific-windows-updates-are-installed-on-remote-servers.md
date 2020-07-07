---
ID: 15227
post_title: >
  Use PowerShell to Determine if Specific
  Windows Updates are Installed on Remote
  Servers
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/05/18/use-powershell-to-determine-if-specific-windows-updates-are-installed-on-remote-servers/
published: true
post_date: 2017-05-18 07:30:17
---
It has been a crazy week to say the least. If you're like me, you wanted to make sure that the specific Windows updates that patch the WannaCry ransomware vulnerability have been installed on all of your servers. I've seen a lot of functions and scripts this week to accomplish that task, but most of them seem too complicated in my opinion.

While it's personal preference, I also always think about whether I should use a PowerShell one-liner, script, or function. Usually one-liners are something I type into the PowerShell console using all the aliases and positional parameters that I want since I'll simply close out of the console when I'm done and the code is gone. Code with aliases and positional parameters shouldn't be saved as scripts or shared with others.

For me, it's a little more difficult to distinguish the difference between whether to use a PowerShell script or function. I write functions as reusable tools that I place into modules which allow me to easily access them. They're generally generic enough to be used in multiple scenarios. Often times, I'll write caller scripts for the functions so the specific data such as server names is not contained within the function itself which makes them easier to share with others outside of my organization.

In the scenario of testing for Windows updates that are installed specifically for WannaCry, I'll use a script since the updates are cumulative and the KB numbers that are valid this month won't be all of the ones that are valid next month that patch this vulnerability. In other words, I chose a script because the shelf life isn't long enough to justify writing a function.

The <em>Get-Hotfix</em> cmdlet is used to check for hotfixes that are installed. It has a <em>ComputerName</em> parameter for targeting remote computers but more than likely it will be blocked by either a network or host firewall since it uses older protocols for communication. Although multiple computer names can be specified with <em>Get-Hotfix</em>, it runs against one computer at a time and it does not continue to the next computer once it tries to connect to one that is unreachable.

The following example demonstrates this problem where <em>Get-Hotfix</em> does not continue to the next computer once it reaches a computer that's unreachable. It also confirms that <em>Get-Hotfix</em> does not run in parallel.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/05/get-hotfix1a.png"><img class="alignnone size-full wp-image-15233" src="http://mikefrobbins.com/wp-content/uploads/2017/05/get-hotfix1a.png" alt="" width="859" height="420" /></a>

Long story short, don't use the <em>ComputerName</em> parameter of <em>Get-Hotfix</em> to query remote computers because there's a better way.

Wrap the <em>Get-Hotfix</em> cmdlet inside <em>Invoke-Command</em> to take advantage of PowerShell remoting. By default, <em>Invoke-Command</em> runs against 32 remote computers at a time in parallel which can be adjusted using the <em>ThrottleLimit</em> parameter. PowerShell remoting is also more firewall friendly and is enabled by default on servers running Windows Server 2012 and higher. It can be enabled on other versions using <em>Enable-PSRemoting</em> as long as PowerShell 2.0 or higher is installed.

The following example scans three servers for the hotfixes listed in <a href="https://technet.microsoft.com/en-us/library/security/ms17-010.aspx" target="_blank" rel="noopener noreferrer">Microsoft Security Bulletin MS17-010</a>.
<pre class="lang:ps decode:true ">Invoke-Command -ComputerName Server01, Server02, Server03 {
    $Patches = 'KB4012598', #Windows XP, Vista, Server 2003, 2008
               'KB4018466', #Server 2008
               'KB4012212', 'KB4012215', 'KB4015549', 'KB4019264', #Windows 7, Server 2008 R2
               'KB4012214', 'KB4012217', 'KB4015551', 'KB4019216', #Server 2012
               'KB4012213', 'KB4012216', 'KB4015550', 'KB4019215', #Windows 8.1, Server 2012 R2
               'KB4012606', 'KB4015221', 'KB4016637', 'KB4019474', #Windows 10
               'KB4013198', 'KB4015219', 'KB4016636', 'KB4019473', 'KB4016871', #Windows 10 1511
               'KB4013429', 'KB4015217', 'KB4015438', 'KB4016635', 'KB4019472' #Windows 10 1607, Server 2016
    Get-HotFix -Id $Patches
} -Credential (Get-Credential) -ErrorAction SilentlyContinue -ErrorVariable Problem

foreach ($p in $Problem) {
    if ($p.origininfo.pscomputername) {
        Write-Warning -Message "Patch not found on $($p.origininfo.pscomputername)" 
    }
    elseif ($p.targetobject) {
        Write-Warning -Message "Unable to connect to $($p.targetobject)"
    }
}</pre>
You could just as easily query Active Directory for the computer names or use <em>Get-Content</em> to obtain a list of computer names from a text file.

I placed the <em>Patches</em> variable inside of <em>Invoke-Command</em> to make the script PowerShell 2.0 compatible. If all of the remote servers were running PowerShell 3.0 or higher, that could have been defined at the top and the <em>Using</em> variable scope modifier could have used to use the local variable in the remote sessions.

Some scripts and functions that I've seen make this process more complicated than it needs to be by first checking to see what operating system and architecture the target computer is running to then only check for the specific updates that are applicable to that OS. There's no reason for that since updates that aren't applicable won't be installed anyway and if any of these updates are found, it's been patched. If you decided to write a function, you could simply return a Boolean value letting you know that the computer is good to go if any one of these updates is found.

µ