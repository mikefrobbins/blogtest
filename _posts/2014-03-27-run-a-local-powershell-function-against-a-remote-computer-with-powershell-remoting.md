---
ID: 9316
post_title: >
  Run a local PowerShell Function against
  a Remote Computer with PowerShell
  Remoting
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/03/27/run-a-local-powershell-function-against-a-remote-computer-with-powershell-remoting/
published: true
post_date: 2014-03-27 07:30:08
---
Did you know that it's super easy to run a function that exists only on your local computer against a remote computer even when no remoting capabilities have been added to the function itself? It does however require that PowerShell remoting be enabled on the remote system, but if you're running Windows Server 2012 or higher, PowerShell remoting is enabled by default on those operating systems.

I'll start off by creating a function that performs a meaningful task so I can use it to demonstrate this process.

In my last blog article “<a href="http://mikefrobbins.com/2014/03/20/use-powershell-to-determine-the-pdc-emulator-fsmo-role-holder-in-your-active-directory-forest-root-domain/" target="_blank">Use PowerShell to Determine the PDC Emulator FSMO Role Holder in your Active Directory Forest Root Domain</a>“, I showed you how to determine which domain controller holds the PDC emulator FSMO role in your Active Directory environment’s forest root domain. This is the domain controller that is ultimately responsible for the time in your Active Directory environment and it should be set to synchronize its time from an external time source. Based on <a href="http://blogs.technet.com/b/nepapfe/archive/2013/03/01/it-s-simple-time-configuration-in-active-directory.aspx" target="_blank">this TechNet article</a>, that source could be a clock device, a router, another standalone server, or an Internet time server.

I’ve created a function named <em>Get-ADTimeSyncSetting</em> as shown in the following code example which retrieves several time related settings that I want to see the values for:
<pre class="lang:ps decode:true" title="Get-ADTimeSyncSetting">function Get-ADTimeSyncSetting {

    $AnnounceFlags = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\config -Name AnnounceFlags
    $MaxNegPhaseCorrection = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\Config -Name MaxNegPhaseCorrection
    $MaxPosPhaseCorrection = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\Config -Name MaxPosPhaseCorrection
    $NtpServer = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\Parameters -Name NtpServer
    $Type = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\Parameters -Name Type
    $SpecialPollInterval = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\NtpClient -Name SpecialPollInterval
    $Enabled = Get-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\W32Time\TimeProviders\NtpServer -Name Enabled

    [PSCustomObject]@{
        AnnounceFlags = $AnnounceFlags.AnnounceFlags
        MaxNegPhaseCorrection = $MaxNegPhaseCorrection.MaxNegPhaseCorrection
        MaxPosPhaseCorrection = $MaxPosPhaseCorrection.MaxPosPhaseCorrection
        NtpServer = $NtpServer.NtpServer
        Type = $Type.Type
        SpecialPollInterval = $SpecialPollInterval.SpecialPollInterval
        Enabled = $Enabled.Enabled
    }
}</pre>
This function has been saved locally in the scripts folder on my Windows 8.1 computer. Since I haven’t added this function to a module yet and it only exists in a ps1 file, I’ll first need to dot source the ps1 file:
<pre class="lang:ps decode:true">. .\Get-ADTimeSyncSetting.ps1</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_10-21-37.png"><img class="alignnone size-full wp-image-9317" alt="2014-03-25_10-21-37" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_10-21-37.png" width="877" height="59" /></a>

I can see that the function now exists locally on the function PSDrive on my Windows 8.1 computer:
<pre class="lang:ps decode:true">Get-ChildItem -Path Function:\Get-ADTimeSyncSetting</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_10-22-03.png"><img class="alignnone size-full wp-image-9318" alt="2014-03-25_10-22-03" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_10-22-03.png" width="877" height="130" /></a>

It's time for the magic of running this local function from my Windows 8.1 computer against the domain controller named dc01 which holds the PDC eumlator FSMO role in the forest root domain in my Active Directory environment:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 ${function:Get-ADTimeSyncSetting}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_11-08-20.png"><img class="alignnone size-full wp-image-9320" alt="2014-03-25_11-08-20" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_11-08-20.png" width="875" height="227" /></a>

Based on the previous results, I can see that these are the default settings and they need to be changed in order for this server to synchronize it's time from an external time source. You may think that this machine is synchronizing its time from <em>time.windows.com</em> since that is defined and shown in the previous results, but that's not the case since "NT5DS" is the type which means to synchronize from the PDC emulator in a domain environment as noted in the previously referenced TechNet blog article.

The process of running a local function against a remote computer looks like magic until you dig into what's happening. First, the <em>-ScriptBlock</em> positional parameter was omitted in the previous example and is shown here which helps in figuring out what's happening:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName dc01 -ScriptBlock ${function:Get-ADTimeSyncSetting}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_13-37-47.png"><img class="alignnone size-full wp-image-9332" alt="2014-03-25_13-37-47" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_13-37-47.png" width="875" height="227" /></a>

The second piece of information that's needed to demystify this magic is seeing what happens when only running the portion of the command with the reference to <em>Get-ADTimeSyncSetting</em> on the Function PSDrive inside of curly braces with the preceding dollar sign:
<pre class="lang:ps decode:true">${function:Get-ADTimeSyncSetting}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_13-41-42.png"><img class="alignnone size-full wp-image-9333" alt="2014-03-25_13-41-42" src="http://mikefrobbins.com/wp-content/uploads/2014/03/2014-03-25_13-41-42.png" width="875" height="346" /></a>

µ