---
ID: 10030
post_title: 'PowerShell Version 5 New Feature: New Parameters added to the New-DscCheckSum and Test-DscConfiguration Cmdlets'
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2014/06/12/powershell-version-5-new-feature-new-parameters-added-to-the-new-dscchecksum-and-test-dscconfiguration-cmdlets/
published: true
post_date: 2014-06-12 07:30:24
---
I'm continuing on my series of blog articles on the new features in the preview version of PowerShell version 5. Today I'll be discussing the existing DSC (Desired State Configuration) cmdlets in PowerShell version 4 that now have new parameters as of the May 2014 preview version of PowerShell version 5.

To begin, I'll define a DSC configuration that's parameterized so that it's reusable:
<pre class="lang:ps decode:true">configuration iSCSI {
	
	param (
		[Parameter(Mandatory)]
		[string[]]$ComputerName
	)
	
	node $ComputerName {
		
        WindowsFeature MultipathIO {
            Name = 'Multipath-IO'
            Ensure = 'Present'
        }
		
        Service iSCSIService {
            Name = 'MSiSCSI'
            StartupType = 'Automatic'
            State = 'Running'
        }
		
    }	
}</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc1a.jpg"><img class="alignnone size-full wp-image-10032" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc1a.jpg" alt="ps5-dsc1a" width="877" height="329" /></a>

Run the configuration specifying the computer names to create MOF files for:
<pre class="lang:ps decode:true">iSCSI -ComputerName SQL01, WEB01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc2.jpg"><img class="alignnone size-full wp-image-10033" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc2.jpg" alt="ps5-dsc2" width="877" height="194" /></a>

In the preview version of PowerShell version 5,  the <em>New-DscCheckSum</em> cmdlet now has a <em>WhatIf</em> parameter:
<pre class="lang:ps decode:true">New-DscCheckSum -ConfigurationPath .\iSCSI -WhatIf</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc3.jpg"><img class="alignnone size-full wp-image-10034" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc3.jpg" alt="ps5-dsc3" width="877" height="84" /></a>

A <em>Confirm</em> parameter has also been added to the <em>New-DscCheckSum</em> cmdlet:
<pre class="lang:ps decode:true">New-DscCheckSum -ConfigurationPath .\iSCSI -Confirm
</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc4.jpg"><img class="alignnone size-full wp-image-10035" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc4.jpg" alt="ps5-dsc4" width="877" height="181" /></a>

Verify the state of the specific features and services on the servers:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL01, WEB01 {
    Get-WindowsFeature -Name Multipath-IO
} | Select-Object -Property PSComputerName, Name, Installed

Invoke-Command -ComputerName SQL01, WEB01 {
    Get-CimInstance -ClassName Win32_Service -Filter "Name = 'MSiSCSI'"
} | Select-Object -Property PSComputerName, Name, State, StartMode</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc5.jpg"><img class="alignnone size-full wp-image-10038" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc5.jpg" alt="ps5-dsc5" width="877" height="312" /></a>

Apply the DSC configuration:
<pre class="lang:ps decode:true">Start-DscConfiguration -ComputerName SQL01, WEB01 -Path .\iSCSI -Wait -Verbose</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc6.jpg"><img class="alignnone size-full wp-image-10040" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc6.jpg" alt="ps5-dsc6" width="877" height="919" /></a>

Check the status of the features and services again:
<pre class="lang:ps decode:true">Invoke-Command -ComputerName SQL01, WEB01 {
    Get-WindowsFeature -Name Multipath-IO
} | Select-Object -Property PSComputerName, Name, Installed

Invoke-Command -ComputerName SQL01, WEB01 {
    Get-CimInstance -ClassName Win32_Service -Filter "Name = 'MSiSCSI'"
} | Select-Object -Property PSComputerName, Name, State, StartMode</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc7.jpg"><img class="alignnone size-full wp-image-10041" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc7.jpg" alt="ps5-dsc7" width="877" height="312" /></a>

Test the DSC configuration using the <em>Test-DscConfiguration</em> cmdlet:
<pre class="lang:ps decode:true">Test-DscConfiguration -CimSession SQL01, WEB01</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc8.jpg"><img class="alignnone size-full wp-image-10042" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc8.jpg" alt="ps5-dsc8" width="877" height="87" /></a>

Note: In the previous example, the <em>CimSession</em> parameter is used even though there isn't a CimSession to SQL01 and WEB01. The computer names can simply be specified in this scenario. This isn't a new feature as this works in PowerShell version 4 as well, but it is something to be aware of so that you don't necessarily have to create a CimSession first.

A <em>Detailed</em> parameter has been added to the <em>Test-DscConfiguration</em> cmdlet in the PowerShell version 5 preview:
<pre class="lang:ps decode:true">Test-DscConfiguration -CimSession WEB01 -Detailed</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc9.jpg"><img class="alignnone size-full wp-image-10044" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc9.jpg" alt="ps5-dsc9" width="877" height="133" /></a>
<pre class="lang:ps decode:true">Test-DscConfiguration -CimSession WEB01 -Detailed | Select-Object -ExpandProperty ResourceID</pre>
<a href="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc10.jpg"><img class="alignnone size-full wp-image-10045" src="http://mikefrobbins.com/wp-content/uploads/2014/06/ps5-dsc10.jpg" alt="ps5-dsc10" width="877" height="87" /></a>
<p style="color: #333333;">This blog article has been written based on the preview version of PowerShell version 5 from <a href="http://blogs.msdn.com/b/powershell/archive/2014/05/14/windows-management-framework-5-0-preview-may-2014-is-now-available.aspx" target="_blank">the May 2014 release of the Windows Management Framework 5.0 preview</a> so what you’ve seen here is subject to change in the final release version.</p>
<p style="color: #333333;">This blog article is one of many that I’ve written and will be writing about the new features in PowerShell version 5. You can find my other blog articles on PowerShell version 5 <a href="http://mikefrobbins.com/tag/powershell-version-5/" target="_blank">here</a>.</p>
µ