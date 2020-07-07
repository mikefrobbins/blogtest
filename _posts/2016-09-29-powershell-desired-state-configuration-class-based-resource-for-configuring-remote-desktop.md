---
ID: 14565
post_title: >
  PowerShell Desired State Configuration
  Class Based Resource for Configuring
  Remote Desktop
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/09/29/powershell-desired-state-configuration-class-based-resource-for-configuring-remote-desktop/
published: true
post_date: 2016-09-29 07:30:10
---
Prior to PowerShell version 5 being released, I had written a PowerShell version 4 compatible DSC (Desired State Configuration) resource named cMrRDP for configuring Remote Desktop. It can be found in <a href="https://github.com/mikefrobbins/DSC" target="_blank">my DSC respository on GitHub</a>. The recommendation at that point was to use the letter "c" as the prefix for community created DSC resources. The current recommendation is to no longer use the "c" prefix for DSC resources. <a href="http://twitter.com/StevenMurawski" target="_blank">Steven Murawski</a> wrote a blog article titled "<em><a href="http://stevenmurawski.com/powershell/2015/06/dsc-people-lets-stop-using-c-now/index.html" target="_blank">DSC People - Let's Stop Using 'c' Now</a></em>" that I recommend taking a look at.

The cMrRDP DSC resource only had the three required functions, <em>Get-TargetResource</em>, <em>Set-TargetResource</em>, and <em>Test-TargetResource</em> and lots of the code was duplicated between the functions. A better approach is to create private functions within the resource that the required functions call. This design eliminates code duplication, makes troubleshooting easier, and simplifies both the code and the functions themselves. Since the functions are simpler, writing unit test for them is also simpler.

I've rewritten that resource as a class based DSC resource and renamed it as MrRemoteDesktop. This resource can also be found in <a href="https://github.com/mikefrobbins/DSC" target="_blank">my DSC repository on GitHub</a>. Class based DSC resources require PowerShell version 5 on the system used for authoring the resource and on the system that the configuration is going to be applied to. With class based resources, Get(), Set(), and Test() methods take the place of the previously referenced required functions. One huge benefit to class based resources is it doesn't require a MOF file for the resource itself. This makes writing the resource simpler and modifications no longer require major rework or starting from scratch.

The following code example has been saved as MrRemoteDesktop.psm1 in the "$env:ProgramFiles\WindowsPowerShell\Modules\MrRemoteDesktop\" folder.
<pre class="lang:ps decode:true" title="MrRemoteDesktop.psm1">enum Ensure {
    Absent
    Present
}
enum UserAuthenication {
    NonSecure
    Secure
}

[DscResource()]
class RemoteDesktop {

    [DscProperty(Key)]
    [UserAuthenication]$UserAuthenication

    [DscProperty(Mandatory)]
    [Ensure]$Ensure

    [RemoteDesktop]Get() {

        $this.UserAuthenication = $this.GetAuthSetting()
        $this.Ensure = $this.GetTSSetting()

	    Return $this

    }

    [void]Set(){

        if ($this.TestAuthSetting() -eq $false) {
            $this.SetAuthSetting($this.UserAuthenication)            
        }

        if ($this.TestTSSetting() -eq $false) {
            $this.SetTSSetting($this.Ensure)            
        }

    }

    [bool]Test(){

        if ($this.TestAuthSetting() -and $this.TestTSSetting() -eq $true) {
            Return $true
        }
        else {
            Return $false
        }

    }

    [string]GetAuthSetting(){
        
        $AuthCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' |
                              Select-Object -ExpandProperty UserAuthentication

        $AuthSetting = switch ($AuthCurrentSetting) {
            0 {'NonSecure'; Break}
            1 {'Secure'; Break}
        }

	    Return $AuthSetting

    }

    [string]GetTSSetting(){

        $TSCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' |
                            Select-Object -ExpandProperty fDenyTSConnections

        $TSSetting = switch ($TSCurrentSetting) {
            0 {'Present'; Break}
            1 {'Absent'; Break}
        }

	    Return $TSSetting

    }

    [void]SetAuthSetting([UserAuthenication]$UserAuthenication){

        switch ($this.UserAuthenication) {
            'NonSecure' {$Script:AuthDesiredSetting = 0; Break}
            'Secure' {$Script:AuthDesiredSetting = 1; Break}
        }

        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' -Value $Script:AuthDesiredSetting

    }

    [void]SetTSSetting([Ensure]$Ensure){

        switch ($this.Ensure) {
            'Present' {$Script:TSDesiredSetting = 0; Break}
            'Absent' {$Script:TSDesiredSetting = 1; Break}
        }

        Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' -Value $Script:TSDesiredSetting

    }

    [bool]TestAuthSetting(){

        if ($this.UserAuthenication -eq $this.GetAuthSetting()){
            Return $true
        }
        else {
            Return $false
        }

    }

    [bool]TestTSSetting(){

        if ($this.Ensure -eq $this.GetTSSetting()){
            Return $true
        }
        else {
            Return $false
        }

    }
    
}</pre>
You'll also need a module manifest file. The module manifest shown in the following example has been saved as MrRemoteDesktop.psd1 in the same folder as the script module file.
<pre class="lang:ps decode:true " title="RemoteDesktop.psd1">@{

# Script module or binary module file associated with this manifest.
RootModule = 'MrRemoteDesktop.psm1'

DscResourcesToExport = 'RemoteDesktop'

# Version number of this module.
ModuleVersion = '1.0'

# ID used to uniquely identify this module
GUID = '30fa4e0e-5233-4eae-ac2d-853e7b9fd8dc'

# Author of this module
Author = 'Mike F Robbins'

# Company or vendor of this module
CompanyName = 'mikefrobbins.com'

# Copyright statement for this module
Copyright = '(c) 2016 Mike F Robbins. All rights reserved.'

# Description of the functionality provided by this module
Description = 'Module with DSC Resource for enabling RDP access to a Computer'

# Minimum version of the Windows PowerShell engine required by this module
PowerShellVersion = '5.0'

}</pre>
Remote Desktop is currently enabled on the server named SQL01:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop1a.png"><img class="alignnone size-full wp-image-14574" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop1a.png" alt="dsc-remote-desktop1a" width="677" height="343" /></a>

This server has the server core (no-GUI) installation of Windows Server 2012 R2 and I don't want admins using remote desktop to connect to it. They should either use PowerShell to remotely administer it or install the GUI tools on their workstation or a server that's dedicated for management of other systems (what I call a jump box).

A configuration to disable RDP is written, the MOF file for SQL01 is generated, and it's applied using pull mode:
<pre class="lang:ps decode:true ">Configuration RDP {
    Import-DSCResource -ModuleName MrRemoteDesktop

    Node SQL01 {

        RemoteDesktop RDP {
            UserAuthenication = 'Secure'
            Ensure = 'Absent'
        }

    }
}

RDP
Start-DscConfiguration -Path .\RDP -ComputerName SQL01 -Wait -Verbose -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop2a.png"><img class="alignnone size-full wp-image-14575" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop2a.png" alt="dsc-remote-desktop2a" width="859" height="526" /></a>

Now that the DSC configuration has been applied to SQL01, remote desktop is disabled on it:

<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop3a.png"><img class="alignnone size-full wp-image-14576" src="http://mikefrobbins.com/wp-content/uploads/2016/09/dsc-remote-desktop3a.png" alt="dsc-remote-desktop3a" width="677" height="343" /></a>

Found a bug or a better way of accomplishing something shown in the code used in this blog article? Feel free to contribute by forking the repository and submitting a pull request.

Update: Be sure to read the follow-up to this blog article: "<a href="http://mikefrobbins.com/2016/10/06/simplifying-my-powershell-version-5-class-based-dsc-resource-for-configuring-remote-desktop/" target="_blank"><em>Simplifying my PowerShell version 5 Class Based DSC Resource for Configuring Remote Desktop</em></a>".

µ