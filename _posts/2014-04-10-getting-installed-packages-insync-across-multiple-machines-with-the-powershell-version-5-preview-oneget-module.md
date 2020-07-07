---
ID: 9517
post_title: >
  Getting Installed Packages InSync Across
  Multiple Machines with the PowerShell
  version 5 Preview OneGet Module
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/04/10/getting-installed-packages-insync-across-multiple-machines-with-the-powershell-version-5-preview-oneget-module/
published: true
post_date: 2014-04-10 07:30:53
---
I presented a session for the <a href="http://mspsug.com/2014/04/01/mike-f-robbins-presenting-managing-active-directory-with-powershell-for-mspsug-on-april-8th-at-830pm-cdt/" target="_blank">Mississippi PowerShell User Group</a> a couple of days ago and I thought I would share a couple of things I showed during that presentation that I hadn't previously blogged about.

I'm starting where I left off during <a href="http://mikefrobbins.com/2014/04/08/using-the-powershell-version-5-preview-oneget-module-with-powershell-remoting/" target="_blank">my last blog article about the OneGet module</a> in the PowerShell version 5 preview.

I've stored the names of the packages that are installed on the local computer (PC03) in a variable named Software and created PSSessions to three remote computers (PC04, PC05, and PC06). Those PSSessions have been stored in a variable named Sessions as shown in the following example:
<pre class="lang:ps decode:true">($software = Get-Package | Select-Object -ExpandProperty Name)
($Session = New-PSSession -ComputerName pc04, pc05, pc06)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_11-16-44.png"><img class="alignnone size-full wp-image-9518" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_11-16-44.png" alt="2014-04-09_11-16-44" width="877" height="218" /></a>

In the previous blog article, the same packages that are installed on PC03 were installed on PC04, PC05, and PC06 using PowerShell remoting and cmdlets from the OneGet module.

Scenario: There has been a recent problem where other admins have made unauthorized changes, adding and removing various packages on PC04, PC05, and PC06.

The <em>Get-Package</em> cmdlet which is part of the new OneGet module can be used to determine what packages are missing and have been added to those machines based off of what's currently installed on the template machine (PC03), all without leaving your desk:
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {
    $InstalledPackages = Get-Package | Select-Object -ExpandProperty Name
    foreach ($package in $Using:software) {
        if ($installedPackages -notcontains $package) {
            Write-Output "$Env:COMPUTERNAME is missing the $package package"
        }
    }
    foreach ($package in $InstalledPackages) {
        if ($Using:software -notcontains $package) {
            Write-Output "Unauthorized package named $package detected on $Env:COMPUTERNAME"
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-47-51.png"><img class="alignnone size-full wp-image-9540" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-47-51.png" alt="2014-04-09_12-47-51" width="877" height="252" /></a>

There is indeed a problem which needs to be corrected to get those machines back in sync with what packages should be installed on them. A slight modification to the previous script has been made to not only report the inconsistencies, but to also correct them on the fly:
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {
    $InstalledPackages = Get-Package | Select-Object -ExpandProperty Name
    foreach ($package in $Using:software) {
        if ($installedPackages -notcontains $package) {
            Write-Output "$Env:COMPUTERNAME is missing the $package package"
            Install-Package -Name $package -Force
        }
    }
    foreach ($package in $InstalledPackages) {
        if ($Using:software -notcontains $package) {
            Write-Output "Unauthorized package named $package detected on $Env:COMPUTERNAME"
            Uninstall-Package -Name $package -Force
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-50-01.png"><img class="alignnone size-full wp-image-9541" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-50-01.png" alt="2014-04-09_12-50-01" width="877" height="371" /></a>

PC04, PC05, and PC06 are now back to having the same packages installed on them as PC03:
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {Get-Package} | Format-Table -GroupBy PSComputerName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-09-35.png"><img class="alignnone size-full wp-image-9536" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_12-09-35.png" alt="2014-04-09_12-09-35" width="877" height="446" /></a>

I've gone back and made some more changes to PC04, PC05, and PC06 to see if I could solve this problem without iterating through both sides with a foreach loop which seems inefficient. This time, multiple issues exist on some of the machines:

<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_13-34-23.png"><img class="alignnone size-full wp-image-9545" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_13-34-23.png" alt="2014-04-09_13-34-23" width="877" height="504" /></a>

Notice in the previous example that using the <em>Format-Table</em> cmdlet with the <em>-GroupBy</em> parameter didn't properly group them by PSComputerName because we have two groups for PC05, although it appeared to work two examples ago. This is actually a bonus tip. To determine why this occurred, take a look at the help for the <em>-GroupBy</em> parameter:
<pre class="lang:ps decode:true">help Format-Table -Parameter GroupBy</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_13-49-29.png"><img class="alignnone size-full wp-image-9550" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_13-49-29.png" alt="2014-04-09_13-49-29" width="877" height="370" /></a>

Based on the information in the help for the <em>-GroupBy</em> parameter, the results needed to be sorted first (via the <em>Sort-Object</em> cmdlet). We just got lucky in the example where it worked that the results were in the correct order. Moral of the story: When in doubt, read the help.

Now back to getting those packages on PC04, PC05, and PC06 in sync with the ones installed on PC03.

Instead of iterating through all of the items installed on the source and comparing them to the destination and then iterating through them again to compare the destination to the source to figure out what's missing and what's installed that shouldn't be, how about getting the differences and iterating through only the differences one time?
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {
    $Differences = Compare-Object -ReferenceObject $Using:software -DifferenceObject (Get-Package | Select-Object -ExpandProperty Name)
    foreach ($Difference in $Differences) {
        if ($Difference.SideIndicator -eq '&lt;=') {
            Write-Output "$Env:COMPUTERNAME is missing the $($Difference.InputObject) package"
            Install-Package -Name $Difference.InputObject -Force
        }
        elseif ($Difference.SideIndicator -eq '=&gt;'){
            Write-Output "Unauthorized package named $($Difference.InputObject) detected on $Env:COMPUTERNAME"
            Uninstall-Package -Name $Difference.InputObject -Force
        }
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_20-37-51.png"><img class="alignnone size-full wp-image-9568" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_20-37-51.png" alt="2014-04-09_20-37-51" width="877" height="408" /></a>

That was definitely a more efficient way of accomplishing this task. All of the machines are back in Sync once again as far as the installed packages go:
<pre class="lang:ps decode:true">Invoke-Command -Session $Session {Get-Package} |
Sort-Object -Property PSComputerName |
Format-Table -GroupBy PSComputerName</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_14-01-47.png"><img class="alignnone size-full wp-image-9552" src="http://mikefrobbins.com/wp-content/uploads/2014/04/2014-04-09_14-01-47.png" alt="2014-04-09_14-01-47" width="877" height="478" /></a>

Notice that in the previous example, the results were sorted via the <em>Sort-Object</em> cmdlet prior to sending them to the <em>Format-Table</em> cmdlet with the <em>-GroupBy</em> parameter to prevent the issue of having multiple groups per computer.

µ