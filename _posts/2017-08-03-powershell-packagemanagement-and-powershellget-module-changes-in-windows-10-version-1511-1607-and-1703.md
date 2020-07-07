---
ID: 15444
post_title: >
  PowerShell PackageManagement and
  PowerShellGet Module Changes in Windows
  10 Version 1511, 1607, and 1703
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/08/03/powershell-packagemanagement-and-powershellget-module-changes-in-windows-10-version-1511-1607-and-1703/
published: true
post_date: 2017-08-03 07:30:07
---
Recently, I reloaded my computer and noticed a problem when I tried to install the latest version of the Pester PowerShell module using PowerShellGet. I loaded Windows 10 version 1703 (the creators update) which has PowerShell version 5.1 installed by default:
<pre class="lang:ps decode:true ">Get-CimInstance -ClassName Win32_OperatingSystem -Property Caption, BuildNumber, OSArchitecture |
Select-Object -Property @{label='Operating System';expression={$_.Caption}},
                        @{label='Version';expression={Get-ItemPropertyValue -Path 'HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion' -Name ReleaseId}},
                        BuildNumber,                        
                        OSArchitecture

$PSVersionTable.PSVersion</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check1b.jpg"><img class="alignnone size-full wp-image-15483" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check1b.jpg" alt="" width="859" height="272" /></a>

Pester version 3.4.0 ships with both Windows 10 version 1607 and 1703. Both of these versions of Windows and/or PowerShell seem to work differently than previous versions when updating and/or installing modules using PowerShellGet as shown in this blog article. I wouldn't necessarily call this a problem, it seems more like increased security, but it negates the effectiveness of the <em>Force</em> parameter in some cases.

The first error when I tried to run <em>Update-Module</em> to update the Pester module is expected since it wasn't originally installed using <em>Install-Module</em>, although it would have been nice of Microsoft to install it that way so updating would have been a little easier.

With Windows 10 version 1511, running <em>Install-Module</em> with the <em>-Force</em> parameter installs the updated module without a problem, but beginning with Windows 10 1607, it generates an error because Pester version 3.4.0 is signed by Microsoft and the versions in the PowerShell Gallery are either not signed or are signed by a different publisher.
<pre class="lang:ps decode:true ">Get-Module -Name Pester -ListAvailable

Update-Module -Name Pester -Force

Install-Module -Name Pester -Force

Install-Module -Name Pester -Force -SkipPublisherCheck

Get-Module -Name Pester -ListAvailable</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check2a.jpg"><img class="alignnone size-full wp-image-15446" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check2a.jpg" alt="" width="859" height="609" /></a>

<span style="color: #ff0000;"><em>"PackageManagement\Install-Package : The version '4.0.5' of the module 'Pester' being installed is not catalog signed.</em></span>
<span style="color: #ff0000;"><em>Ensure that the version '4.0.5' of the module 'Pester' has the catalog file 'Pester.cat' and signed with the same</em></span>
<span style="color: #ff0000;"><em>publisher 'CN=Microsoft Root Certificate Authority 2010, O=Microsoft Corporation, L=Redmond, S=Washington, C=US' as</em></span>
<span style="color: #ff0000;"><em>the previously-installed module '4.0.5' with version '3.4.0' under the directory 'C:\Program</em></span>
<span style="color: #ff0000;"><em>Files\WindowsPowerShell\Modules\Pester\3.4.0'. If you still want to install or update, use -SkipPublisherCheck </em></span><span style="color: #ff0000;"><em>parameter.</em></span>
<span style="color: #ff0000;"><em>At C:\Program Files\WindowsPowerShell\Modules\PowerShellGet\1.0.0.1\PSModule.psm1:1809 char:21</em></span>
<span style="color: #ff0000;"><em>+ ... $null = PackageManagement\Install-Package @PSBoundParameters</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (Microsoft.Power....InstallPackage:InstallPackage) [Install-Package], Exception</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : ModuleIsNotCatalogSigned,Validate-ModuleAuthenticodeSignature,Microsoft.PowerShell.Packa</em></span><span style="color: #ff0000;"><em>geManagement.Cmdlets.InstallPackage"</em></span>

Let's see who signed the modules to verify the previous errors are valid:
<pre class="lang:ps decode:true">Get-Module -Name Pester -ListAvailable -PipelineVariable Module |
Select-Object -Property @{label='FilePath';expression={$_.Path}} |
Get-AuthenticodeSignature |
Format-Table -AutoSize -Wrap -Property @{label='Name';expression={$Module.Name}},
                                       @{label='Version';expression={$Module.Version}},
                                       Status,
                                       @{label='SignedBy';expression={$_.SignerCertificate.Issuer -replace '^.*O=|,.*$'}}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check3b.jpg"><img class="alignnone size-full wp-image-15449" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check3b.jpg" alt="" width="859" height="222" /></a>

Now to show the behavior in Windows 10 version 1511 which has PowerShell version 5.0 installed by default:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check4b.jpg"><img class="alignnone size-full wp-image-15485" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check4b.jpg" alt="" width="859" height="276" /></a>

As you can see, the <em>SkipPublisherCheck</em> parameter wasn't previously required:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check5a.jpg"><img class="alignnone size-full wp-image-15451" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check5a.jpg" alt="" width="859" height="287" /></a>

<span style="color: #ff0000;"><em>"Update-Module : Module 'Pester' was not installed by using Install-Module, so it cannot be updated.</em></span>
<span style="color: #ff0000;"><em>At line:1 char:1</em></span>
<span style="color: #ff0000;"><em>+ Update-Module -Name Pester -Force</em></span>
<span style="color: #ff0000;"><em>+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</em></span>
<span style="color: #ff0000;"><em> + CategoryInfo : InvalidOperation: (Pester:String) [Write-Error], WriteErrorException</em></span>
<span style="color: #ff0000;"><em> + FullyQualifiedErrorId : ModuleNotInstalledUsingInstallModuleCmdlet,Update-Module"</em></span>

Pester version 3.3.5 instead of version 3.4.0 ships with Windows 10 version 1511 so I thought there might be some differences with the signatures, but they're the same:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check6a.jpg"><img class="alignnone size-full wp-image-15452" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check6a.jpg" alt="" width="859" height="228" /></a>

I originally thought that maybe the PackageManagement or PowerShellGet module had changed, but both of those are at version 1.0.0.1 on Windows 10 version 1511, 1607, and 1703. Be sure to read the update at the end of this blog article.

I'm also seeing subtle differences when trying to install a module that includes commands that already exist on your system. On Windows 10 version 1511 with PowerShell 5.0, the PowerShell Community Extensions module installs with no problem:
<pre class="lang:ps decode:true">Get-Command -Name GCB

Install-Module -Name Pscx -Force</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check7a.jpg"><img class="alignnone size-full wp-image-15459" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check7a.jpg" alt="" width="859" height="142" /></a>

On Windows 10 version 1607 or 1703, it complains about needing to use the <em>AllowClobber</em> parameter because a command in the Pscx module already exists on the system (That same command also existed in version 1511).
<pre class="lang:ps decode:true">Get-Command -Name GCB

Install-Module -Name Pscx -Force

Install-Module -Name Pscx -Force -AllowClobber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check8a.jpg"><img class="alignnone size-full wp-image-15460" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check8a.jpg" alt="" width="859" height="272" /></a>

<em><span style="color: #ff0000;">"PackageManagement\Install-Package : A command with name 'gcb' is already available on this system. This module 'Pscx'</span></em>
<em><span style="color: #ff0000;">may override the existing commands. If you still want to install this module 'Pscx', use -AllowClobber parameter.</span></em>
<em><span style="color: #ff0000;">At C:\Program Files\WindowsPowerShell\Modules\PowerShellGet\1.0.0.1\PSModule.psm1:1661 char:21</span></em>
<em><span style="color: #ff0000;">+ ... $null = PackageManagement\Install-Package @PSBoundParameters</span></em>
<em><span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~</span></em>
<em><span style="color: #ff0000;"> + CategoryInfo : InvalidOperation: (Microsoft.Power....InstallPackage:InstallPackage) [Install-Package], </span></em><em><span style="color: #ff0000;">Exception</span></em>
<em><span style="color: #ff0000;"> + FullyQualifiedErrorId : CommandAlreadyAvailable,Validate-ModuleCommandAlreadyAvailable,Microsoft.PowerShell.Pack</span></em>
<em><span style="color: #ff0000;"> ageManagement.Cmdlets.InstallPackage"</span></em>

I don't have a problem with the increased security, but my complaint is that whatever changes Microsoft has made, while great for security are breaking changes and has effectively rendered the <em>Force</em> parameter useless for the cmdlets shown in this blog article. When <em>Force</em> is specified, it means remove all safeties because I know what I'm doing and I don't care if you have to burn my system to the ground if that's what it takes to make it so. In other words, the <em>Force</em> parameter should override the need for the <em>SkipPublisherCheck</em> and/or the <em>AllowClobber</em> parameter in the scenarios shown in this blog article.<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/updated.jpg"><img class="alignnone size-full wp-image-15480" src="http://mikefrobbins.com/wp-content/uploads/2017/08/updated.jpg" alt="" width="424" height="283" /></a>I decided to perform a little more testing after noticing the line numbers in the error messages shown in this blog article were different depending on whether or not the command was run on Windows 10 version 1607 or 1703. I also decided to republish this blog article with different title to reflect the results of this further testing.

It appears what has happened to cause these differences, is that the PackageManagement and PowerShellGet modules have indeed been changed between Windows 10 versions and their module version numbers haven't been updated which made tracking down the source of the differences a little more difficult.

Windows 10 version 1511:
<pre class="lang:ps decode:true ">Get-ChildItem -Path (Get-Module -Name PowerShellGet, PackageManagement -ListAvailable).ModuleBase</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check9b.jpg"><img class="alignnone size-full wp-image-15487" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check9b.jpg" alt="" width="859" height="587" /></a>

Windows 10 version 1607:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check10b.jpg"><img class="alignnone size-full wp-image-15488" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check10b.jpg" alt="" width="859" height="584" /></a>

Windows 10 version 1703:

<a href="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check11b.jpg"><img class="alignnone size-full wp-image-15489" src="http://mikefrobbins.com/wp-content/uploads/2017/08/module-publisher-check11b.jpg" alt="" width="859" height="584" /></a>

The file dates could be different and still be the same file, but the sizes are different which is a dead giveaway that something somewhere has changed.

µ