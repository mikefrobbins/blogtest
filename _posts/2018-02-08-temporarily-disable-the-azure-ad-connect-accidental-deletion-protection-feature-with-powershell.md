---
ID: 16163
post_title: >
  Temporarily Disable the Azure AD Connect
  Accidental Deletion Protection Feature
  with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2018/02/08/temporarily-disable-the-azure-ad-connect-accidental-deletion-protection-feature-with-powershell/
published: true
post_date: 2018-02-08 07:30:34
---
You've implemented <a href="https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect" target="_blank" rel="noopener">Azure AD Connect</a> to synchronize accounts in your on-premises Active Directory environment to Azure AD. If you took the defaults while running the setup wizard for Azure AD Connect, then everything in your Active Directory environment is synchronized. If you decided to filter the synchronization later to only specific OU's (Organizational Units) in your Active Directory environment, you could run into a scenario where the number of deletions exceeds the default threshold of 500 objects.

If this occurs, you'll receive an email stating the following:

<em>The Identity synchronization service detected that the number of deletions exceeded the configured deletion threshold. A total of <strong>1004</strong> objects were sent for deletion in this Identity synchronization run. This met or exceeded the configured deletion threshold value of <strong>500</strong> objects. </em><em>We need you to provide confirmation that these deletions should be processed before we will proceed. </em><em>Please see <a href="https://azure.microsoft.com/en-US/documentation/articles/active-directory-aadconnectsync-feature-prevent-accidental-deletes/" target="_blank" rel="noopener">Preventing Accidental Deletions</a> for more information about the error listed in this email message.</em>

There's a way to see which objects are about to be deleted as shown in the support article referenced in the information contained in that email. You can run the necessary commands directly from the machine that the Azure AD Connect is installed on. Log into a server with RDP, are you kidding me? While I could simply using the <em>Enter-PSSession</em> cmdlet to establish a PowerShell One-To-One remoting session, I decided to use implicit remoting instead to accomplish this task.

In addition to using implicit remoting, why not use PowerShell Core 6.0 while we're at it, although this particular version of PowerShell certainly isn't required.

First, I'll stored my Azure credentials that have the necessary rights to perform this task in a variable.
<pre class="lang:ps decode:true">$AzureCred = Get-Credential</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync1a.jpg"><img class="alignnone size-full wp-image-16164" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync1a.jpg" alt="" width="979" height="163" /></a>

I'll also store admin credentials for the on-premises server running Azure AD Connect in a variable.
<pre class="lang:ps decode:true">$Cred = Get-Credential</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync2a.jpg"><img class="alignnone size-full wp-image-16165" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync2a.jpg" alt="" width="979" height="164" /></a>

I'll use a PowerShell one-liner to both create a PSSession to my on-premises server running Azure AD Connect and import the ADSync module with Implicit remoting.
<pre class="lang:ps decode:true">Import-PSSession -Session (New-PSSession -ComputerName srv01 -Credential $Cred) -Module ADSync</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync3a.jpg"><img class="alignnone size-full wp-image-16166" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync3a.jpg" alt="" width="979" height="162" /></a>

My recommendation is to always check the value of an item before making a change to it so you can always get back to where you started.
<pre class="lang:ps decode:true">Get-ADSyncExportDeletionThreshold -AADCredential $AzureCred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync4a.jpg"><img class="alignnone size-full wp-image-16167" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync4a.jpg" alt="" width="979" height="208" /></a>

Either increase the threshold or disable the setting altogether while the mass deletion occurs. Proceed at your own risk.
<pre class="lang:ps decode:true">Disable-ADSyncExportDeletionThreshold -AADCredential $AzureCred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync5a.jpg"><img class="alignnone size-full wp-image-16168" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync5a.jpg" alt="" width="979" height="68" /></a>

Verify the setting is indeed disabled.
<pre class="lang:ps decode:true">Get-ADSyncExportDeletionThreshold -AADCredential $AzureCred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync6a.jpg"><img class="alignnone size-full wp-image-16169" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync6a.jpg" alt="" width="979" height="209" /></a>

Once the deletions have completed, be sure to re-enable the protection otherwise it's an accident waiting for a place to happen.
<pre class="lang:ps decode:true">Enable-ADSyncExportDeletionThreshold -AADCredential $AzureCred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync7a.jpg"><img class="alignnone size-full wp-image-16170" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync7a.jpg" alt="" width="979" height="65" /></a>

Last, but not least, verify the protection is indeed enabled.
<pre class="lang:ps decode:true ">Get-ADSyncExportDeletionThreshold -AADCredential $AzureCred</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync4a.jpg"><img class="alignnone size-full wp-image-16167" src="http://mikefrobbins.com/wp-content/uploads/2018/01/aad-sync4a.jpg" alt="" width="979" height="208" /></a>

µ