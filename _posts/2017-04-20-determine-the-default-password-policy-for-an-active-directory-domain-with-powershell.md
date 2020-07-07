---
ID: 15171
post_title: >
  Determine the Default Password Policy
  for an Active Directory Domain with
  PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2017/04/20/determine-the-default-password-policy-for-an-active-directory-domain-with-powershell/
published: true
post_date: 2017-04-20 07:30:18
---
I've been working with PowerShell since the version 1.0 days and I'm still amazed that I find cmdlets that I didn't know existed. Back in 2003, I had written some PowerShell code to query group policy for the lockout policy of an Active Directory domain. It used code similar to what's shown in the following example which requires the GroupPolicy PowerShell module that installs as part of the RSAT (<a href="https://www.microsoft.com/en-us/download/details.aspx?id=45520" target="_blank" rel="noopener noreferrer">Remote Server Administration Tools</a>).
<pre class="lang:ps decode:true">(([xml](Get-GPOReport -Name "Default Domain Policy" -ReportType Xml)
).GPO.Computer.ExtensionData.Extension.Account |
Where-Object name -eq LockoutBadCount).SettingNumber</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy1a.png"><img class="alignnone size-full wp-image-15172" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy1a.png" alt="" width="859" height="97" /></a>

I recently discovered that there's a <em>Get-ADDefaultDomainPasswordPolicy</em> cmdlet that's part of the ActiveDirectory PowerShell module that also installs as part of the RSAT.
<pre class="lang:ps decode:true">Get-ADDefaultDomainPasswordPolicy</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy2a.png"><img class="alignnone size-full wp-image-15173" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy2a.png" alt="" width="859" height="264" /></a>

You could select only the LockoutThreshold property to return the same results as shown in the first example:
<pre class="lang:ps decode:true ">(Get-ADDefaultDomainPasswordPolicy).LockoutThreshold</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy3a.png"><img class="alignnone size-full wp-image-15174" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy3a.png" alt="" width="859" height="71" /></a>

The default lockout threshold for active directory accounts is 0 which means they're never locked out. That's not good so it's something you might want to consider adding to your operational readiness testing for your infrastructure. The following example is a <a href="https://github.com/pester/Pester/wiki" target="_blank" rel="noopener noreferrer">Pester</a> test that checks this setting and verifies that it's not set to zero.
<pre class="lang:ps decode:true">Describe 'LockoutThreshold' {
    It 'Should NOT be zero' {
        (Get-ADDefaultDomainPasswordPolicy).LockoutThreshold |
        Should Not Be 0
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy4a.png"><img class="alignnone size-full wp-image-15176" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy4a.png" alt="" width="859" height="180" /></a>

Once you correct the problem by changing the account lockout threshold to a value greater than zero, the test should pass.

<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy5a.png"><img class="alignnone size-full wp-image-15177" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy5a.png" alt="" width="859" height="152" /></a>

I like that Pester shows how long it took to execute the test. This tells me that using the <em>Get-ADDefaultDomainPasswordPolicy</em> is not only easier to use, but it's also more efficient.
<pre class="lang:ps decode:true">Describe 'LockoutThreshold' {
    It 'Should NOT be zero' {
        (([xml](Get-GPOReport -Name "Default Domain Policy" -ReportType Xml)
        ).GPO.Computer.ExtensionData.Extension.Account |
        Where-Object name -eq LockoutBadCount).SettingNumber |
        Should Not Be 0
    }
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy6a.png"><img class="alignnone size-full wp-image-15187" src="http://mikefrobbins.com/wp-content/uploads/2017/04/lockout-policy6a.png" alt="" width="859" height="178" /></a>

µ