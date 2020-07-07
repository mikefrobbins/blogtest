---
ID: 18431
post_title: >
  Default to Device Authentication when
  Connecting to Azure with Windows
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2020/07/02/default-to-device-authentication-when-connecting-to-azure-with-windows-powershell/
published: true
post_date: 2020-07-02 07:30:32
---
Windows 10 Enterprise edition version 2004 is used for the scenarios demonstrated in this blog article. If you'd like to follow along, you'll also need to <a href="https://docs.microsoft.com/powershell/scripting/install/installing-powershell" target="_blank" rel="noopener noreferrer">install PowerShell version 7</a> and <a href="https://docs.microsoft.com/powershell/azure/install-az-ps" target="_blank" rel="noopener noreferrer">the Az PowerShell module</a>.

As stated in the help for <em><a href="https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount" target="_blank" rel="noopener noreferrer">Connect-AzAccount</a></em>, the <em>UseDeviceAuthentication</em> parameter is the default authentication type for PowerShell version 6 and higher.
<pre class="lang:ps decode:true">help Connect-AzAccount -Parameter UseDeviceAuthentication</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth1a.jpg"><img class="alignnone size-full wp-image-18432" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth1a.jpg" alt="" width="1103" height="360" /></a>

What this means is that you're provided with a URL and a code. They're used to authenticate with Azure when signing in interactively from PowerShell.
<pre class="lang:ps decode:true">Connect-AzAccount</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth2a.jpg"><img class="alignnone size-full wp-image-18434" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth2a.jpg" alt="" width="1103" height="122" /></a>

By default, Windows PowerShell prompts you to sign in with a GUI:
<pre class="lang:ps decode:true">Connect-AzAccount</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth3a.jpg"><img class="alignnone size-full wp-image-18435" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth3a.jpg" alt="" width="1103" height="661" /></a>

Replicating the default behavior from PowerShell 7 in Windows PowerShell is easy enough using the <em>UseDeviceAuthentication</em> parameter:
<pre class="lang:ps decode:true">Connect-AzAccount -UseDeviceAuthentication</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth4a.jpg"><img class="alignnone size-full wp-image-18436" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth4a.jpg" alt="" width="1103" height="123" /></a>

To set this type of behavior as the default in Windows PowerShell, add the <em>UseDeviceAuthentication</em> parameter to your <a href="https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_parameters_default_values" target="_blank" rel="noopener noreferrer"><em>$PSDefaultParameterValues</em> preference variable</a>:
<pre class="lang:ps decode:true">$PSDefaultParameterValues['Connect-AzAccount:UseDeviceAuthentication']=$true</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth5a.jpg"><img class="alignnone size-full wp-image-18437" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth5a.jpg" alt="" width="1102" height="223" /></a>

Once added, anytime that you sign in to Azure from Windows PowerShell, it will default to the same behavior as PowerShell 7:
<pre class="lang:ps decode:true">Connect-AzAccount</pre>
<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth6a.jpg"><img class="alignnone size-full wp-image-18438" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth6a.jpg" alt="" width="1102" height="121" /></a>

<strong>Troubleshooting</strong>

The values stored in <em>$PSDefaultParameterValues</em> don't persist between PowerShell sessions. Adding the items to your <a href="https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_profiles">PowerShell profile</a> is the key to making them persist.

Use <em>$PSDefaultParameterValues</em> with caution. It could lock you into a particular parameter set as is the case with the scenario outlined in this blog article.

Don't enclose Boolean values in quotes that you add to <em>$PSDefaultParameterValues</em>. Using quotes causes the values to be added, but not work properly.

<a href="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth7a.jpg"><img class="alignnone size-full wp-image-18439" src="https://mikefrobbins.com/wp-content/uploads/2020/07/az-deviceauth7a.jpg" alt="" width="1102" height="292" /></a>

<strong>Additional Information</strong>
<ul>
 	<li>The end of life for <a href="https://docs.microsoft.com/powershell/scripting/powershell-support-lifecycle#powershell-releases-end-of-life" target="_blank" rel="noopener noreferrer">PowerShell version 6</a> is September 4, 2020. Update to PowerShell version 7.</li>
 	<li><a href="https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az">Migrate from the AzureRM to the Az PowerShell module</a>. AzureRM will continue to receive bug fixes until at least December 2020.</li>
</ul>
µ