---
ID: 14619
post_title: >
  Simplifying my PowerShell version 5
  Class Based DSC Resource for Configuring
  Remote Desktop
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/10/06/simplifying-my-powershell-version-5-class-based-dsc-resource-for-configuring-remote-desktop/
published: true
post_date: 2016-10-06 07:30:25
---
Last week I wrote a blog article about a "<a href="http://mikefrobbins.com/2016/09/29/powershell-desired-state-configuration-class-based-resource-for-configuring-remote-desktop/" target="_blank"><em>PowerShell Desired State Configuration Class Based Resource for Configuring Remote Desktop</em></a>". Since then I've discovered and learned a couple of new things about enumerations in PowerShell that can be used to simply the code even further.

My original code used a couple of enumerations which I've removed to show how they can be used to further simply the code:
<pre class="lang:ps decode:true">class RemoteDesktop {

    [DscProperty(Key)]
    [string]$UserAuthenication

    [DscProperty(Mandatory)]
    [string]$Ensure

    [RemoteDesktop]Get() {

        $this.UserAuthenication = $this.GetAuthSetting()
        $this.Ensure = $this.GetTSSetting()

	    Return $this

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
    
}</pre>
The code shown in the previous example uses switch statements to translate the numeric values returned from the registry into human readable names. There are several different ways to instantiate a copy of the RemoteDesktop class and call the <em>Get()</em> method:
<pre class="lang:ps decode:true ">(New-Object -TypeName RemoteDesktop).Get()

([RemoteDesktop]::new()).Get()

$RDP = [RemoteDesktop]::new()
$RDP.Get()</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum1a.png"><img class="alignnone size-full wp-image-14621" src="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum1a.png" alt="rdp-class-enum1a" width="859" height="307" /></a>

I'll remove the switch statements to show that numeric values are indeed returned without them:
<pre class="lang:ps decode:true ">class RemoteDesktop {

    [DscProperty(Key)]
    [string]$UserAuthenication

    [DscProperty(Mandatory)]
    [string]$Ensure

    [RemoteDesktop]Get() {

        $this.UserAuthenication = $this.GetAuthSetting()
        $this.Ensure = $this.GetTSSetting()

	    Return $this

    }

    [string]GetAuthSetting(){
        
        $AuthCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' |
                              Select-Object -ExpandProperty UserAuthentication

	    Return $AuthCurrentSetting

    }

    [string]GetTSSetting(){

        $TSCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' |
                            Select-Object -ExpandProperty fDenyTSConnections

	    Return $TSCurrentSetting

    }
    
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum2a.png"><img class="alignnone size-full wp-image-14624" src="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum2a.png" alt="rdp-class-enum2a" width="859" height="127" /></a>

My original code from the previously referenced blog article used enumerations instead of ValidateSet, but I've since learned that enumerations in PowerShell offer a lot more functionality than just input validation.
<pre class="lang:ps decode:true ">enum UserAuthenication {
    NonSecure
    Secure
}
enum Ensure {
    Absent
    Present
}

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

    [UserAuthenication]GetAuthSetting(){
        
        $AuthCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' |
                              Select-Object -ExpandProperty UserAuthentication

	    Return $AuthCurrentSetting

    }

    [Ensure]GetTSSetting(){

        $TSCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' |
                            Select-Object -ExpandProperty fDenyTSConnections

	    Return $TSCurrentSetting

    }
    
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum3a.png"><img class="alignnone size-full wp-image-14626" src="http://mikefrobbins.com/wp-content/uploads/2016/09/rdp-class-enum3a.png" alt="rdp-class-enum3a" width="859" height="129" /></a>

By simply using the enumerations, the values are automatically translated from their numeric values returned from the registry into their human readable names that are defined in the enumeration so there's no need to perform the translation with switch statements.

The only problem at this point is the incorrect value is being return by the Ensure enumeration. By default the first item in the enumeration is 0, the second one is 1, and so on. I could simply swap the order of the two items in the Ensure enumeration to correct this problem:
<pre class="lang:ps decode:true">enum Ensure {
    Present
    Absent
}</pre>
A better option is to be more declarative and define the value of each item in the enumerations so the order doesn't matter:
<pre class="lang:ps decode:true ">enum UserAuthenication {
    NonSecure = 0
    Secure = 1
}
enum Ensure {
    Absent = 1
    Present = 0
}</pre>
Although the code was already simple, the end result after refactoring it is even simpler code and less of it:
<pre class="lang:ps decode:true " title="MrRemoteDesktop.psm1">enum UserAuthenication {
    NonSecure = 0
    Secure = 1
}
enum Ensure {
    Present = 0
    Absent = 1
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
            Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' -Value $this.UserAuthenication        
        }

        if ($this.TestTSSetting() -eq $false) {
            Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' -Value $this.Ensure        
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

    [UserAuthenication]GetAuthSetting(){
        
        $AuthCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name 'UserAuthentication' |
                              Select-Object -ExpandProperty UserAuthentication

	    Return $AuthCurrentSetting

    }

    [Ensure]GetTSSetting(){

        $TSCurrentSetting = Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name 'fDenyTSConnections' |
                            Select-Object -ExpandProperty fDenyTSConnections

	    Return $TSCurrentSetting

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
As shown in the previous code example, I also decided to move the code from the <em>SetAuthSetting()</em> and <em>SetTSSetting()</em> methods back into the actual <em>Set()</em> method since other methods don't call them which means that there's no redundant code eliminated by separating them and separating them adds complexity.

The updated version of the PowerShell Desired State Configuration Class Based Resource for Configuring Remote Desktop shown in this blog article can be downloaded from <a href="https://github.com/mikefrobbins/DSC" target="_blank">my DSC repository on GitHub</a>.

µ