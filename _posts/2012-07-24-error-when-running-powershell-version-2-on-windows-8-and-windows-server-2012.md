---
ID: 4826
post_title: >
  Error When Running PowerShell -Version 2
  on Windows 8 and Windows Server 2012
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/24/error-when-running-powershell-version-2-on-windows-8-and-windows-server-2012/
published: true
post_date: 2012-07-24 07:30:42
---
<strong>Windows 8 Release Preview</strong>

On a fresh installation of the Windows 8 Release Preview, when attempting to run <em>PowerShell.exe -Version 2</em>, you'll receive the following error:

"Version v2.0.50727 of the .NET Framework is not installed and it is required to run version 2 of Windows PowerShell."

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-3.jpg"><img class="alignnone size-full wp-image-4834" title="ps-v2error-3" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-3.jpg" alt="" width="640" height="54" /></a>

The following error message is generated if you attempt to run the same command from the PowerShell ISE:

<em><span style="color: #ff0000;">"powershell : V e r s i o n v 2 . 0 . 5 0 7 2 7 o f t h e . N E T F r a m e w o r k </span></em>
<em> <span style="color: #ff0000;"> i s n o t i n s t a l l e d a n d i t i s r e q u i r e d t o r u n </span></em>
<em> <span style="color: #ff0000;"> v e r s i o n 2 o f W i n d o w s P o w e r S h e l l . </span></em>
<em> <span style="color: #ff0000;">At line:1 char:1</span></em>
<em> <span style="color: #ff0000;">+ powershell -v 2</span></em>
<em> <span style="color: #ff0000;">+ ~~~~~~~~~~~~~~~</span></em>
<em> <span style="color: #ff0000;"> + CategoryInfo : NotSpecified: (V e r s i o n ... r S h e l l . :String) [], Remote </span></em>
<em> <span style="color: #ff0000;"> Exception</span></em>
<em> <span style="color: #ff0000;"> + FullyQualifiedErrorId : NativeCommandError"</span></em>

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-1.jpg"><img class="alignnone size-full wp-image-4827" title="ps-v2error-1" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-1.jpg" alt="" width="640" height="178" /></a>

The solution is to enable the .NET Framework 3.5 feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-21.jpg"><img class="alignnone size-full wp-image-4831" title="ps-v2error-21" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-21.jpg" alt="" width="425" height="373" /></a>

You'll need Internet access to enable this feature because the source files don't exist on the machine (the State is "DisabledWithPayloadRemoved"):

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-29.jpg"><img class="alignnone size-full wp-image-4877" title="ps-v2error-29" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-29.jpg" alt="" width="595" height="156" /></a>

I'm glad to see the new <em>Get-WindowsOptionalFeature</em>, <em>Enable-WindowsOptionalFeature</em>, and <em>Disable-WindowsOptionalFeature</em> cmdlets in Windows 8 so I no longer have to use Deployment Image Servicing and Management (DISM) command line commands:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-295.jpg"><img class="alignnone size-full wp-image-5038" title="ps-v2error-295" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-295.jpg" alt="" width="553" height="152" /></a>

Once the .NET Framework feature is enabled, PowerShell -Version 2 works without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-35.jpg"><img class="alignnone size-full wp-image-4883" title="ps-v2error-35" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-35.jpg" alt="" width="592" height="201" /></a>

<strong>Windows Server 2012 Release Candidate</strong>

The same issue exists in both the Server Core &amp; GUI installation of Windows Server 2012:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-3.jpg"><img class="alignnone size-full wp-image-4834" title="ps-v2error-3" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-3.jpg" alt="" width="640" height="54" /></a>

The resolution is also the same, enable the .NET Framework 3.5 feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-41.jpg"><img class="alignnone size-full wp-image-5014" title="ps-v2error-41" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-41.jpg" alt="" width="640" height="451" /></a>

Whether these features are installed or not can also be determined in PowerShell:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-51.jpg"><img class="alignnone size-full wp-image-4882" title="ps-v2error-51" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-51.jpg" alt="" width="640" height="162" /></a>

The strange thing about this issue on Server 2012 is the "PowerShell-V2" feature shows as being removed without any changes being made to a default installation of Windows Server 2012. One other interesting bit of information is shown in the following screenshot, unlike Windows Server 2008 R2, the PoweShell ISE is installed by default on a 2012 Server when a GUI installation is performed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-55.jpg"><img class="alignnone size-full wp-image-5001" title="ps-v2error-55" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-55.jpg" alt="" width="640" height="153" /></a>

Other than the ISE not being installed, these options are the same on 2012 Server Core:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-9.jpg"><img class="alignnone size-full wp-image-4889" title="ps-v2error-9" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-9.jpg" alt="" width="588" height="166" /></a>

Once the "NET-Framework-Core" feature is enabled, Powershell v2 works without issue:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-61.jpg"><img class="alignnone size-full wp-image-5002" title="ps-v2error-61" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-61.jpg" alt="" width="595" height="177" /></a>

Notice that I used the <em>Install-WindowsFeature</em> cmdlet in the previous example. The <em>Install-WindowsFeature</em> and <em>Uninstall-WindowsFeature</em> cmdlets are new to Server 2012 and replace the <em>Add-WindowsFeature</em> and <em>Remove-WindowsFeature</em> cmdlets. The old cmdlet names are now aliases to these new cmdlets:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-65.jpg"><img class="alignnone size-full wp-image-5003" title="ps-v2error-65" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-65.jpg" alt="" width="637" height="141" /></a>

When the 'NET-Framework-Core" feature is enabled, it also appears to enable the "PowerShell-V2" feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-111.jpg"><img class="alignnone size-full wp-image-5022" title="ps-v2error-111" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-111.jpg" alt="" width="640" height="153" /></a>

I decided to test further since there's no logical reason adding the .NET Framework should enable the PowerShell version 2 feature. I removed both the "NET-Framework-Core" and "Powershell-V2" features:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-121.jpg"><img class="alignnone size-full wp-image-5004" title="ps-v2error-121" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-121.jpg" alt="" width="592" height="141" /></a>

After restarting as specified in the previous step, I enabled the "NET-Framework-Core" feature which did not automatically enable the "PowerShell-V2" feature. My guess is the "PowerShell-V2" feature didn't show up as being enabled before because of the missing .NET Framework prerequisite and once that prerequisite was added, it showed as enabled.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-131.jpg"><img class="alignnone size-full wp-image-5005" title="ps-v2error-131" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-131.jpg" alt="" width="640" height="296" /></a>

I removed both features again and added just the "PowerShell-V2" feature and as expected, it automatically enabled the "NET-Framework-Core" prerequisite feature:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-141.jpg"><img class="alignnone size-full wp-image-5006" title="ps-v2error-141" src="http://mikefrobbins.com/wp-content/uploads/2012/07/ps-v2error-141.jpg" alt="" width="640" height="353" /></a>

µ