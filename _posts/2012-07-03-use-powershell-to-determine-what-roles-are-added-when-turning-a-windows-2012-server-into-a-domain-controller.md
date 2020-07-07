---
ID: 4554
post_title: >
  Use PowerShell to Determine What Roles
  are Added When Turning a Windows 2012
  Server into a Domain Controller
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2012/07/03/use-powershell-to-determine-what-roles-are-added-when-turning-a-windows-2012-server-into-a-domain-controller/
published: true
post_date: 2012-07-03 07:30:14
---
Goal: Determine what roles are installed when turning a Windows Server 2012 machine into a domain controller.

I started out by using PowerShell to save a list of what roles are installed on a plain vanilla 2012 server that has the full GUI installation. The following one liner would be used in PowerShell version 2 to accomplish this task and the syntax is compatible with version 3:
<pre class="lang:ps decode:true">Get-WindowsFeature | Where-Object {$_.Installed} | Select-Object Name | Out-File c:\tmp\pre-ad-install.txt</pre>
PowerShell version 3 has simplified syntax when using the Where-Object cmdlet with a single condition. Here's the one liner I used to save this information:
<pre class="lang:ps decode:true">Get-WindowsFeature | Where-Object Installed | Select-Object Name | Out-File c:\tmp\pre-ad-install.txt</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-1.jpg"><img class="alignnone size-full wp-image-4555" title="2012dc-1" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-1.jpg" width="640" height="54" /></a>

Now to install the Active Directory domain services role from Server Manager. Click on "Add roles and features":

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-2.jpg"><img class="alignnone size-full wp-image-4556" title="2012dc-2" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-2.jpg" width="473" height="247" /></a>

Select the "Active Directory Domain Services" role:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-3.jpg"><img class="alignnone size-full wp-image-4557" title="2012dc-3" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-3.jpg" width="549" height="301" /></a>

This message about dependencies is displayed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-4.jpg"><img class="alignnone size-full wp-image-4558" title="2012dc-4" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-4.jpg" width="429" height="445" /></a>

A few features are automatically selected such as "Group Policy Management" which was also listed on the dependencies popup screen as shown in the previous image.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-5.jpg"><img class="alignnone size-full wp-image-4559" title="2012dc-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-5.jpg" width="549" height="406" /></a>

If you wait long enough on the installation screen, you can promote the server to a domain controller by clicking on the link shown in the image below:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-55.jpg"><img class="alignnone size-full wp-image-4632" title="2012dc-55" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-55.jpg" width="640" height="450" /></a>

It's possible to close the installation window shown in the previous image before the install is complete and it will finish in the background. It's also easy to miss the link to turn it into a domain controller. If either one happens, you'll end up back on the Server Manager screen:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-6.jpg"><img class="alignnone size-full wp-image-4560" title="2012dc-6" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-6.jpg" width="640" height="229" /></a>

I didn't click on the link to promote the server to a domain controller because at this point I wanted to know what roles had been added since we started:
<pre class="lang:ps decode:true">Compare-Object -ReferenceObject (Get-Content c:\tmp\gui-pre-ad-install.txt) -DifferenceObject (Get-Content c:\tmp\gui-post-addsrole-pre-dc.txt)</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-71.jpg"><img class="alignnone size-full wp-image-4603" title="2012dc-71" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-71.jpg" width="640" height="165" /></a>

Since the server isn't a domain controller yet, clicking on AD DS on the left side of the screen shows that additional configuration is necessary:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-8.jpg"><img class="alignnone size-full wp-image-4562" title="2012dc-8" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-8.jpg" width="640" height="162" /></a>

Click on the "Promote this server to a domain controller" link:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-9.jpg"><img class="alignnone size-full wp-image-4563" title="2012dc-9" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-9.jpg" width="640" height="287" /></a>

I'm creating a new Active Directory forest named mikefrobbins.com:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-10.jpg"><img class="alignnone size-full wp-image-4564" title="2012dc-10" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-10.jpg" width="575" height="301" /></a>

If the "Domain Name System (DNS) server" capability option is unselected on this screen, the DNS Server role will not be installed on the server:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-11.jpg"><img class="alignnone size-full wp-image-4565" title="2012dc-11" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-11.jpg" width="638" height="385" /></a>

The cool new feature of this entire process is the "View Script" button in the bottom right hand corner of this screen:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-12.jpg"><img class="alignnone size-full wp-image-4566" title="2012dc-12" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-12.jpg" width="640" height="366" /></a>

Clicking on this button displays the PowerShell script that would complete this portion of the installation process:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-13.jpg"><img class="alignnone size-full wp-image-4567" title="2012dc-13" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-13.jpg" width="404" height="365" /></a>

At the end of the installation, the following message is displayed briefly and then the server is automatically restarted:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-16.jpg"><img title="2012dc-16" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-16.jpg" width="640" height="273" /></a>

After the restart, the server is now a domain controller and the following roles have been added since the last time we checked what roles had been installed:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-141.jpg"><img class="alignnone size-full wp-image-4605" title="2012dc-141" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-141.jpg" width="640" height="102" /></a>

The following roles have been installed during this entire process:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-151.jpg"><img class="alignnone size-full wp-image-4606" title="2012dc-151" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-151.jpg" width="640" height="229" /></a>

Beginning with Windows Server 2012, the recommended and default installation type is server core (no-GUI) so I decided to copy the PowerShell script that was created during the previous process to a server core machine. A line to add the Active Directory Domain services role has been added to the following script because it would fail otherwise:
<pre class="lang:ps decode:true">#
# Windows PowerShell script for AD DS Deployment
#
Add-WindowsFeature AD-Domain-Services
Import-Module ADDSDeployment
Install-ADDSForest `
-CreateDnsDelegation:$false `
-DatabasePath "C:\Windows\NTDS" `
-DomainMode "Win2012" `
-DomainName "mikefrobbins.com" `
-DomainNetbiosName "MIKEFROBBINS" `
-ForestMode "Win2012" `
-InstallDns:$true `
-LogPath "C:\Windows\NTDS" `
-NoRebootOnCompletion:$true `
-SysvolPath "C:\Windows\SYSVOL" `
-Force:$true</pre>
This script was saved as "create-domain.ps1" and was run on the server core machine:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-17.jpg"><img class="alignnone size-full wp-image-4570" title="2012dc-17" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-17.jpg" width="640" height="155" /></a>

The restart option was also changed in the script so the server doesn't automatically restart upon installation completion.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-18.jpg"><img class="alignnone size-full wp-image-4571" title="2012dc-18" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-18.jpg" width="640" height="283" /></a>

The following roles were added using this script. The File-Services and FS-FileServer roles don't show up until after a restart:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-192.jpg"><img class="alignnone size-full wp-image-4612" title="2012dc-192" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-192.jpg" width="640" height="158" /></a>

Here's a list of what roles were installed while turning a GUI 2012 server into a domain controller that were not automatically installed on the server core machine:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-202.jpg"><img class="alignnone size-full wp-image-4611" title="2012dc-202" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-202.jpg" width="608" height="121" /></a>

Those missing roles are available to install on server core:

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-21.jpg"><img class="alignnone size-full wp-image-4615" title="2012dc-21" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-21.jpg" width="640" height="151" /></a>

I installed them just to confirm this, although I wouldn't recommend installing them on your production servers because you won't need them anyway if you're following the best practice of not managing this sort of stuff directly on your servers.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-22.jpg"><img class="alignnone size-full wp-image-4616" title="2012dc-22" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-22.jpg" width="640" height="139" /></a>

Additional Information: If I unselected the "Include management tools (if applicable)" option on the screen shown in the following image during the GUI installation, the only role that would have been installed on the GUI server that's not installed on the Server Core machine is the "GPMC" role.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-23.jpg"><img class="alignnone size-full wp-image-4638" title="2012dc-23" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/07/2012dc-23.jpg" width="427" height="445" /></a>

Unchecking this management tools option as referenced above makes the Group Policy Management option unselected on the screen shown below during the installation, but the GPMC role is still installed on the server whether it is selected or not.

<a href="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-5.jpg"><img class="alignnone size-full wp-image-4559" title="2012dc-5" alt="" src="http://mikefrobbins.com/wp-content/uploads/2012/06/2012dc-5.jpg" width="549" height="406" /></a>

This blog is based off of the release candidate version of Windows Server 2012 and things could change between this version and RTM.

µ