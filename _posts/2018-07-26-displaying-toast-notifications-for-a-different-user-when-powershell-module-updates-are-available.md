---
ID: 16760
post_title: >
  Displaying Toast Notifications for a
  Different User when PowerShell Module
  Updates are Available
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/07/26/displaying-toast-notifications-for-a-different-user-when-powershell-module-updates-are-available/
published: true
post_date: 2018-07-26 07:30:08
---
A couple of months ago, <a href="https://twitter.com/WindosNZ" target="_blank" rel="noopener">Josh King</a> presented a session on “<a href="https://mspsug.com/2018/06/19/video-using-the-powershell-burnttoast-module-to-display-timely-notifications/" target="_blank" rel="noopener">Using BurntToast to Display Timely Notifications</a>” for our June 2018 <a href="https://twitter.com/MSPSUG" target="_blank" rel="noopener">Mississippi PowerShell User Group</a> virtual meeting. I was previously planning to write something to display balloon notifications in Windows and I learned that they're now called toast notifications. I also learned that Josh had created a module named <a href="https://www.powershellgallery.com/packages/BurntToast/" target="_blank" rel="noopener">BurntToast</a> which performs most of the heavy lifting so I could simply take advantage of it instead of writing my own code.
<blockquote>Note: This blog article is written using Windows 10 version 1803 and Windows PowerShell version 5.1. Your mileage may vary with different operating systems and/or different versions of PowerShell.</blockquote>
First, install the BurntToast module from the PowerShell Gallery.
<pre class="lang:ps decode:true ">Install-Module -Name BurntToast -Force</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast1a.png"><img class="alignnone size-full wp-image-16762" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast1a.png" alt="" width="859" height="58" /></a>

The BurntToast module contains several functions, but the one I'm interested in is <em>New-BurntToastNotification</em>.
<pre class="lang:ps decode:true ">Get-Command -Module BurntToast</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast2a.png"><img class="alignnone size-full wp-image-16763" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast2a.png" alt="" width="859" height="309" /></a>

A simple test notification revealed that notifications can't be displayed for a different user. I log into my computer as a standard user and then run PowerShell elevated as a different user account.
<pre class="lang:ps decode:true ">New-BurntToastNotification -Text 'There are new PowerShell module updates available.'</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast3a.png"><img class="alignnone size-full wp-image-16765" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast3a.png" alt="" width="859" height="151" /></a>

There didn't seem to be an option built into the BurntToast module to work around this problem so I resorted to using the <em>Runas.exe</em> command from within PowerShell to display the test notification for a different user.

The first time <em>Runas.exe</em> is used with the <em>Savecred</em> parameter, it prompts for the specified user's password and saves it in credential manager.
<pre class="lang:ps decode:true">runas.exe /user:mikefrobbins\user01 /savecred powershell.exe</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast4a.png"><img class="alignnone size-full wp-image-16766" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast4a.png" alt="" width="859" height="80" /></a>

From that point forward, it will use the saved credentials for the specified user as long as they're valid.
<pre class="lang:ps decode:true ">runas.exe /user:mydomain\mrobbins /savecred "C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe -NoProfile -WindowStyle Hidden -Command New-BurntToastNotification -Text 'There are new PowerShell module updates available.'"</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast5a.png"><img class="alignnone size-full wp-image-16767" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast5a.png" alt="" width="859" height="69" /></a>

Success! The previous command displays the following toast notification.

<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast6a.png"><img class="alignnone size-full wp-image-16769" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast6a.png" alt="" width="366" height="102" /></a>

While using the <em>Runas.exe</em> command achieves the desired result, there has to be a PowerShell way to accomplish this task and indeed there is.
<pre class="lang:ps decode:true">$UserCred = Get-Credential
Start-Process -FilePath "$env:windir\System32\WindowsPowerShell\v1.0\powershell.exe" -ArgumentList "-NoProfile -Command New-BurntToastNotification -Text 'There are new PowerShell module updates available.'" -WindowStyle Hidden -Credential $UserCred</pre>
I installed <a href="https://twitter.com/Jaykul" target="_blank" rel="noopener">Joel Bennett</a>'s <a href="https://www.powershellgallery.com/packages/BetterCredentials/" target="_blank" rel="noopener">BetterCredentials</a> PowerShell module from the PowerShell Gallery so I could easily read credentials from credential manager instead of being prompted for them or storing them in a variable or file.
<pre class="lang:ps decode:true ">Install-Module -Name BetterCredentials -Force -AllowClobber</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast8a.png"><img class="alignnone size-full wp-image-16787" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast8a.png" alt="" width="859" height="58" /></a>

If you haven't already, store the credentials in credential manager for the user who is logged into Windows.
<pre class="lang:ps decode:true">Get-Credential -Store</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast9a.png"><img class="alignnone size-full wp-image-16789" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast9a.png" alt="" width="859" height="326" /></a>

Now to wire up the code to perform a check to determine if any PowerShell module updates are available. I'll simply use the <a href="https://mikefrobbins.com/2016/06/16/powershell-function-to-find-information-about-module-updates/" target="_blank" rel="noopener"><em>Find-MrModuleUpdate</em></a> function that I've previously written which is part of <a href="https://www.powershellgallery.com/packages/MrToolkit/" target="_blank" rel="noopener">my MrToolkit PowerShell module</a>.
<pre class="lang:ps decode:true">Install-Module -Name MrToolkit -Force</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast7a.png"><img class="alignnone size-full wp-image-16770" src="https://mikefrobbins.com/wp-content/uploads/2018/07/burnttoast7a.png" alt="" width="859" height="59" /></a>

Finally, I'm going to embed the following code into my PowerShell profile to perform the check and display the toast notification for any/all logged in user(s) if there are any PowerShell module updates available. You also have the option of setting it up as a scheduled task or job instead of adding it to your PowerShell profile.
<pre class="lang:ps decode:true">Start-Job {
    if (-not(Get-Module -Name MrToolkit -ListAvailable)) {
        Install-Module -Name MrModule -Force
    }
    if (-not(Get-Module -Name BurntToast -ListAvailable)) {
        Install-Module -Name BurntToast -Force
    }

    if (Find-MrModuleUpdate) {
        Get-CimInstance -ClassName Win32_ComputerSystem -Property UserName |
        ForEach-Object {
            Start-Process -FilePath 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' -ArgumentList "-NoProfile -Command New-BurntToastNotification -Text 'There are new PowerShell module updates available.'" -WindowStyle Hidden -Credential (Get-Credential -UserName $_.UserName -Store)
        }
    }
} | Out-Null</pre>
It's being run as a job because it's not the fastest thing in the world to check each and every module for updates.

This isn't the type of thing you want running every time you open PowerShell so I've specifically added it to the all users, current host profile for the Windows PowerShell console which is located in the following location: "<em>C:\Windows\System32\WindowsPowerShell\v1.0\Microsoft.PowerShell_profile.ps1</em>". I've also added logic to test for Internet connectivity and only have it run the first time I open the PowerShell console each Tuesday.
<pre class="lang:ps decode:true ">if (Test-NetConnection -ComputerName bing.com -Port 80 -InformationLevel Quiet -ErrorAction SilentlyContinue -WarningAction SilentlyContinue) {
    $Date = Get-Date
        
    if (($Date).DayOfWeek -eq 'Tuesday') {

        $ModuleLUCPath = "$env:ProgramFiles\WindowsPowerShell\Configuration\moduleupdates-lastchecked.txt"

        $ModuleLastUpdateCheck = (Get-ChildItem -Path $ModuleLUCPath -ErrorAction SilentlyContinue).LastWriteTime 

        if ($ModuleLastUpdateCheck.Date -ne $Date.Date) {

            if ((New-Object -TypeName System.Security.Principal.WindowsPrincipal([System.Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([System.Security.Principal.WindowsBuiltInRole]::Administrator)) {

                New-Item -Path $ModuleLUCPath -ItemType File -Force | Out-Null
                
                Start-Job {
                    if (-not(Get-Module -Name MrToolkit -ListAvailable)){
                        Install-Module -Name MrModule -Force
                    }
                    if (-not(Get-Module -Name BurntToast -ListAvailable)){
                        Install-Module -Name BurntToast -Force
                    }

                    if (Find-MrModuleUpdate) {
                        Get-CimInstance -ClassName Win32_ComputerSystem -Property UserName |
                        ForEach-Object {
                            Start-Process -FilePath 'C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe' -ArgumentList "-NoProfile -Command New-BurntToastNotification -Text 'There are new PowerShell module updates available.'" -WindowStyle Hidden -Credential (Get-Credential -UserName $_.UserName -Store)
                        }
                    }
                } | Out-Null

            }
            else {
                Write-Warning -Message 'Aborting due to PowerShell not being run elevated as a local admin!'
            }

        }

    }

}</pre>
I've gone down a rabbit hole and jumped through some hoops in this blog article to display the notifications because of running PowerShell as a different user than I'm logged into Windows as, but I've learned quite a few things in the process that I'm sure will translate to other future tasks.

µ