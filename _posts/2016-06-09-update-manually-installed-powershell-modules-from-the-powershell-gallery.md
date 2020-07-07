---
ID: 14092
post_title: >
  Update Manually Installed PowerShell
  Modules from the PowerShell Gallery
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2016/06/09/update-manually-installed-powershell-modules-from-the-powershell-gallery/
published: true
post_date: 2016-06-09 07:30:25
---
There are PowerShell modules that ship with Windows 10 that weren't installed from <a href="http://www.powershellgallery.com/" target="_blank">the PowerShell Gallery</a> using <a href="http://mikefrobbins.com/2015/04/23/powershellget-the-big-easy-way-to-discover-install-and-update-powershell-modules/" target="_blank">PowerShellGet</a> so they can't be updated using the <em>Update-Module</em> cmdlet. This also applies for any modules that you've installed manually yourself.

The following PowerShell script retrieves a list of the most recent version of the modules in the all users path for PowerShell modules. It determines which ones weren't installed using PowerShellGet based on the hidden xml file that would exist in the module directory and then it determines if a newer version exists in one of the PowerShell Galleries that's registered on your computer:
<pre class="lang:ps decode:true">Get-Module -ListAvailable |
Where-Object ModuleBase -like $env:ProgramFiles\WindowsPowerShell\Modules\* |
Sort-Object -Property Name, Version -Descending |
Get-Unique -PipelineVariable Module |
ForEach-Object {
    if (-not(Test-Path -Path "$($_.ModuleBase)\PSGetModuleInfo.xml")) {
        Find-Module -Name $_.Name -OutVariable Repo -ErrorAction SilentlyContinue |
        Compare-Object -ReferenceObject $_ -Property Name, Version |
        Where-Object SideIndicator -eq '=&gt;' |
        Select-Object -Property Name,
                                Version,
                                @{label='Repository';expression={$Repo.Repository}},
                                @{label='InstalledVersion';expression={$Module.Version}}
    }
    
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/module-update1a.jpg"><img class="alignnone size-full wp-image-14093" src="http://mikefrobbins.com/wp-content/uploads/2016/05/module-update1a.jpg" alt="module-update1a" width="859" height="336" /></a>

Installing the updated version is as simple as piping the previous results to <em>ForEach-Object</em> and nesting <em>Install-Module</em> with the <em>Force</em> parameter inside of it:
<pre class="lang:ps decode:true ">Get-Module -ListAvailable |
Where-Object ModuleBase -like $env:ProgramFiles\WindowsPowerShell\Modules\* |
Sort-Object -Property Name, Version -Descending |
Get-Unique -PipelineVariable Module |
ForEach-Object {
    if (-not(Test-Path -Path "$($_.ModuleBase)\PSGetModuleInfo.xml")) {
        Find-Module -Name $_.Name -OutVariable Repo -ErrorAction SilentlyContinue |
        Compare-Object -ReferenceObject $_ -Property Name, Version |
        Where-Object SideIndicator -eq '=&gt;' |
        Select-Object -Property Name,
                                Version,
                                @{label='Repository';expression={$Repo.Repository}},
                                @{label='InstalledVersion';expression={$Module.Version}}
    }
    
} | ForEach-Object {Install-Module -Name $_.Name -Force}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2016/05/module-update2a.jpg"><img class="alignnone size-full wp-image-14094" src="http://mikefrobbins.com/wp-content/uploads/2016/05/module-update2a.jpg" alt="module-update2a" width="859" height="240" /></a>

Be sure to read the follow-up to this blog article "<em><a href="http://mikefrobbins.com/2016/06/16/powershell-function-to-find-information-about-module-updates/" target="_blank">PowerShell function to find information about module updates</a></em>".

µ